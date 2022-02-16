# Netty学习笔记（三）

## 粘包与半包

粘包

- 现象，发送`abc def`，接收`abcdef`
- 原因
  - 应用层：接收方ByteBuf设置太大（Netty默认1024）
  - 滑动窗口：假设发送方256 bytes表示一个完整报文，但由于接收方处理不及时且窗口大小足够大，这256 bytes字节就会缓冲在接收方的滑动窗口中，当滑动窗口中缓冲了多个报文就会粘包
  - Nagle算法：会造成粘包

半包

- 现象，发送`abcdef`，接收 `abc def`
- 原因
  - 应用层：接收方ByteBuf小于实际发送数量
  - 滑动窗口：假设接收方的窗口只剩下128 bytes，发送方的报文大小是256 bytes，这时放不下了，只能先发送前128 bytes，等待ack后才能发送剩余部分，这就造成了半包
  - MSS限制：当发送数据超过MSS限制后，会将数据切分发送，就会造成半包

本质是因为TCP是流式协议，消息无边界

解决方案

短连接，解决粘包，不能解决半包

定长解码器，缺点占用的字节数比较多

行解码器，使用分割符作为消息边界，缺点效率比较低

LTC解码器

```bas
lengthFieldOffset   —— 长度字段偏移量
lengthFieldLength   —— 长度字段长度
lengthAdjustment    —— 长度字段基准，还有几个字节是内容
initialBytesToStrip —— 从头剥离几个字节
```

```java
public class TestLengthFieldDecoder {
    public static void main(String[] args) {
        EmbeddedChannel channel = new EmbeddedChannel(
                new LengthFieldBasedFrameDecoder(1024, 0, 4, 0, 4),
                new LoggingHandler(LogLevel.DEBUG)
        );

        // 4个字节的内容长度，实际内容
        ByteBuf buf = ByteBufAllocator.DEFAULT.buffer();
        writeToByteBuf(buf, "Hello, World");
        writeToByteBuf(buf, "Hello");
        writeToByteBuf(buf, "Hi");
        channel.writeInbound(buf);
    }

    private static void writeToByteBuf(ByteBuf buf, String content) {
        byte[] bytes = content.getBytes(); // 实际内容
        int length = bytes.length; // 实际内容长度
        buf.writeInt(length);
        buf.writeBytes(bytes);
    }
}
```



## 协议设计与解析

### Redis

```java
/**
 * Redis协议
 * set name zhangsan
 * *3
 * $3
 * set
 * $4
 * name
 * $8
 * zhangsan
 */
@Slf4j
public class TestRedis {
    public static final byte[] LINE = {13, 10};
    public static void main(String[] args) {
        NioEventLoopGroup group = new NioEventLoopGroup();
        try {
            Bootstrap bootstrap = new Bootstrap();
            bootstrap.channel(NioSocketChannel.class);
            bootstrap.group(group);
            bootstrap.handler(new ChannelInitializer<SocketChannel>() {
                @Override
                protected void initChannel(SocketChannel ch) throws Exception {
                    ch.pipeline().addLast(new LoggingHandler());
                    ch.pipeline().addLast(new ChannelInboundHandlerAdapter() {
                        @Override
                        public void channelActive(ChannelHandlerContext ctx) throws Exception {
                            ByteBuf buf = ctx.alloc().buffer();
                            buf.writeBytes("*3".getBytes());
                            buf.writeBytes(LINE);
                            buf.writeBytes("$3".getBytes());
                            buf.writeBytes(LINE);
                            buf.writeBytes("set".getBytes());
                            buf.writeBytes(LINE);
                            buf.writeBytes("$4".getBytes());
                            buf.writeBytes(LINE);
                            buf.writeBytes("name".getBytes());
                            buf.writeBytes(LINE);
                            buf.writeBytes("$8".getBytes());
                            buf.writeBytes(LINE);
                            buf.writeBytes("zhangsan".getBytes());
                            buf.writeBytes(LINE);
                            ctx.writeAndFlush(buf);
                        }

                        @Override
                        public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                            ByteBuf buf = (ByteBuf) msg;
                            log.info(buf.toString(Charset.defaultCharset()));
                        }
                    });
                }
            });
            ChannelFuture future = bootstrap.connect("localhost", 6379).sync();
            future.channel().closeFuture().sync();
        } catch (InterruptedException e) {
            log.error("client error {}", e.getMessage());
        } finally {
            group.shutdownGracefully();
        }
    }
}
```

### Http

```java
@Slf4j
public class TestHttp {
    public static void main(String[] args) {
        NioEventLoopGroup boss = new NioEventLoopGroup();
        NioEventLoopGroup worker = new NioEventLoopGroup();
        try {
            ServerBootstrap serverBootstrap = new ServerBootstrap();
            serverBootstrap.channel(NioServerSocketChannel.class);
            serverBootstrap.group(boss, worker);
            serverBootstrap.childHandler(new ChannelInitializer<SocketChannel>() {
                @Override
                protected void initChannel(SocketChannel socketChannel) throws Exception {
                    socketChannel.pipeline().addLast(new LoggingHandler(LogLevel.DEBUG));
                    socketChannel.pipeline().addLast(new HttpServerCodec());
                    socketChannel.pipeline().addLast(new ChannelInboundHandlerAdapter() {
                        @Override
                        public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                            if (msg instanceof HttpRequest) {
                                HttpRequest request = (HttpRequest) msg;
                                log.debug(request.uri());
                                log.debug(String.valueOf(request.headers()));
                                // 返回响应
                                DefaultFullHttpResponse response = new DefaultFullHttpResponse(request.protocolVersion(), HttpResponseStatus.OK);
                                byte[] bytes = "<h1>Hello, World!</h1>".getBytes(StandardCharsets.UTF_8);
                                response.headers().setInt(CONTENT_LENGTH, bytes.length);
                                response.content().writeBytes(bytes);
                                ctx.writeAndFlush(response);
                            } else if (msg instanceof HttpContent) {

                            }
                        }
                    });
                }
            });
            ChannelFuture future = serverBootstrap.bind(8080).sync();
            future.channel().closeFuture().sync();
        } catch (InterruptedException e) {
            log.error("server error: " + e.getMessage());
        } finally {
            boss.shutdownGracefully();
            worker.shutdownGracefully();
        }
    }
}
```

### 自定义协议

协议要素：

- 魔数，用来在第一时间判定是否是无效数据包
- 版本号，可以支持协议的升级
- 序列化算法，消息正文到底采用哪种序列化反序列化方式，可以由此扩展，例如：json、protobuf、hessian、jdk、……
- 指令类型，是登录、注册、单聊、群聊……，和业务相关
- 请求序号，为了双工通信，提供异步能力
- 正文长度
- 消息正文

```java
@Slf4j
public class MessageCodec extends ByteToMessageCodec<Message> {
    @Override
    protected void encode(ChannelHandlerContext channelHandlerContext, Message message, ByteBuf byteBuf) throws Exception {
        // 1. 4个字节的魔数
        byteBuf.writeBytes(new byte[]{'y', 'u', 'h', 'e'});
        // 2. 1个字节的版本
        byteBuf.writeByte(1);
        // 3. 1个字节的序列化方式 jdk 0 json 1
        byteBuf.writeByte(0);
        // 4. 1个字节的指令类型
        byteBuf.writeByte(message.getMessageType());
        // 5. 4个字节的指令序号
        byteBuf.writeInt(message.getSequenceId());
        // 对齐，填充
        byteBuf.writeByte(0xff);
        // 获取内容的字节数组
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bos);
        oos.writeObject(message);
        byte[] bytes = bos.toByteArray();
        // 6. 长度
        byteBuf.writeInt(bytes.length);
        // 7. 内容
        byteBuf.writeBytes(bytes);
    }

    @Override
    protected void decode(ChannelHandlerContext channelHandlerContext, ByteBuf byteBuf, List<Object> list) throws Exception {
        int magicNum = byteBuf.readInt();
        byte version = byteBuf.readByte();
        byte serializerType = byteBuf.readByte();
        byte messageType = byteBuf.readByte();
        int sequenceId = byteBuf.readInt();
        byteBuf.readByte();
        int length = byteBuf.readInt();
        byte[] bytes = new byte[length];
        byteBuf.readBytes(bytes, 0, length);
        if (serializerType == 0) {
            ObjectInputStream ois = new ObjectInputStream(new ByteArrayInputStream(bytes));
            Message message = (Message) ois.readObject();
            log.debug(message.toString());
            list.add(message);
        }
        log.debug("{}, {}, {}, {}, {}, {}", magicNum, version, serializerType, messageType, sequenceId, length);
    }
}
```

## 连接假死

原因

- 网络设备出现故障，例如网卡、机房网络不稳定等，底层的TCP连接已经断开了，但应用程序没有感知到，仍然占用着资源
- 公网网络不稳定，出现丢包，如果连续出现丢包，这时现象就是客户端数据发不出去，服务端也一直收不到数据，就这么一直耗着
- 应用程序线程阻塞，无法进行数据读写

问题

- 假死的连接占用的资源不能自动释放
- 向假死的连接发送数据，得到的反馈是发送超时

解决方法：

使用空闲连接检测器

```java
// 用来判断是不是 读空闲时间过长 或者 写空闲时间过长
// 5s 内如果没有收到 channel 的数据，会触发一个 IdleState.READER_IDLE 事件
new IdleStateHandler(5, 0, 0);
```



## 扩展序列化算法

序列化，反序列化主要用在消息正文的转换上

- 序列化时，需要将Java对象变为要传输的数据（可以是byte[]，或json等，最终都需要变成byte[]）
- 反序列化时，需要将传入的正文数据还原成Java对象，便于处理

为了支持更多序列化算法，抽象一个`Serializer`接口

```java
public interface Serializer {
    // 反序列化方法
    <T> T deserialize(Class<T> clazz, byte[] bytes);
    // 序列化方法
    <T> byte[] serialize(T object);
}
```



## 参数调优

1）CONNECT_TIMEOUT_MILLIS

- 属于SocketChannel参数
- 用在客户端建立连接时，如果在指定毫秒内无法连接，会抛出timeout异常
- SO_TIMEOUT 主要用在阻塞IO，阻塞IO中accept，read等都是无限等待，如果不希望永远阻塞，使用它调整超时时间

2）SO_BACKLOG

- 属于ServerSocketChannel参数
- 默认值 Windows NT Server 4.0+: 200，Linux and Mac OS X: 128
- netty中，可以通过`option(ChannelOption.SO_BACKLOG, 值)`来设置大小

3）ulimit -n

- 属于操作系统参数
- 临时调整
- 一个进程允许打开的文件描述符数量

4）TCP_NODELAY

- 属于SocketChannel参数
- 默认false，开启了Nagle算法
- 建议设为true

5）SO_SNDBUF & SO_RCVBUF

- SO_SNDBUF：属于SocketChannel参数
- SO_RCVBUF：既可用于SocketChannel参数，也可以用于ServerSocketChannel参数（建议设置到ServerSocketChannel上）

6）ALLOCATOR

- 属于SocketChannel参数
- 用来分配ByteBuf、ctx.alloc()

7）RCVBUF_ALLOCATOR

- 属于SocketChannel参数
- 控制netty接收缓冲区大小
- 负责入站数据的分配，决定入站缓冲区的大小（并可动态调整），统一采用direct直接内存，具体池化还是非池化由allocator决定



## Netty源码

TODO: 阅读Netty源码

- [ ] 启动流程
- [ ] EventLoop
- [ ] accept流程
- [ ] read流程
- [ ] ……

