# HLA pRTI开发

## FederateAmbassador and the RTIambassador
有两个主要的 Java 接口来处理联邦的开发，FederateAmbassador 和 RTIambassador。

FederateAmbassador 接口是 RTI 与联邦进行通信的接口。 

RTI 通过调用 FederateAmbassador 类中的方法向联邦发送消息（调用这样的方法通常被称为调用回调）。

当您编写自己的联邦时，需要实现所有可用回调方法的类来实现 FederateAmbassador 接口。还有一个方便的类 NullFederateAmbassador，它只是提供 FederateAmbassdor 接口中所有回调方法的空实现。当您开发自己的联邦时，您可以简单地继承 NullFederateAmbassador 并实现（覆盖）您感兴趣的回调。

RTIambassador 是联邦与 RTI 通信的类。代码如下所示：

```java
RtiFactory rtiFactory = RtiFactoryFactory.getRtiFactory();
_rtiAmbassador = rtiFactory.getRtiAmbassador();
```

第一行是检索 RtiFactory 的默认方式（多个 RTI-factories 可能可用，具体取决于计算机上安装的 RTI）。第二行调用 RtiFactory 以获取 RTIambassador。

联邦和 RTI 之间的所有通信都必须通过 FederateAmbassador 和 RTIambassador 接口。

## Connecting to the RTI

在创建和加入联邦执行之前，联邦必须连接到 RTI。这是通过以下代码完成的：

```java
String settingsDesignator = "crcAddress=" + rtiHost + ":" +
Integer.toString(CRC_PORT);
_rtiAmbassador.connect(this, CallbackModel.HLA_IMMEDIATE, settingsDesignator);
```

请注意，设置指示符字符串也可以留空或包含对本地设置指示符的抽象引用名称。

## The Federation Object Model

每个联合必须有一个或多个 FOM 文件（扩展名为 .xml），这些文件描述了要在联合中使用的对象和交互。可以使用 Visual OMT™ 1516 等轻松创建文件。

需要做的第一件事是创建联合执行。创建联合执行服务将一个或多个 FOM 文件作为参数。这可以由任何联邦成员来完成。如果已存在具有指定名称的联合执行，则会引发异常并且必须捕获该异常。使用以下代码完成创建：

```java
File fddFile = new File("Chat-evolved.xml");
try {
	_rtiAmbassador.createFederationExecution("ChatRoom", fddFile.toURL());
} catch (FederationExecutionAlreadyExists ignored) {
}
```

参数是联合的名称 (ChatRoom) 和在这种情况下使用的单个 FOM 文件的 URL 表示 (Chat-evolved.xml)。

创建联邦时，联邦必须加入联邦。这是通过以下代码完成的：

```java
FederateHandle federateHandle = _rtiAmbassador.joinFederationExecution("Chat",
"ChatType", "ChatRoom", new URL[]{fddFile.toURL()});
```

前三个参数分别是联邦成员的名称、联邦成员的类型和要加入的联邦的名称。第四个参数是联邦成员想要带到联邦的 FOM 文件的 URL 数组。在我们的例子中，添加 FOM 模块是不必要的，因为我们已经使用相同的 FOM 模块创建了联邦，所以这只是说明如何在加入时添加模块的一种方式。

联邦成员现在是该联邦的成员。请注意，创建联邦执行的联邦不会自动加入该执行。

对象和交互用于在联邦中的联邦之间交换数据。

对象有属性，交互有参数，用来描述它们的特性。本指南仅介绍交互作用和参数的使用。

每个交互和参数都由一个句柄表示。句柄是通过 RTIambassador 从 RTI 获得的，如下面的代码所示。

```java
InteractionClassHandle _messageId;
ParameterHandle _parameterIdText;
ParameterHandle _parameterIdSender;
_messageId = _rtiAmbassador.getInteractionClassHandle("Communication");
_parameterIdText = _rtiAmbassador.getParameterHandle(_messageId, "Message");
_parameterIdSender = _rtiAmbassador.getParameterHandle(_messageId, "Sender");
```

getInteractionClassHandle 调用的参数是 FOM 文件中指定的交互的名称。 getParameterHandle 调用中的第一个参数是参数所属的交互，第二个是参数的名称。

句柄是联邦对交互和参数的表示，例如可以在发送和接收交互时使用。

## Publishing and Subscribing to Information

数据交换由数据的发布和订阅控制。对于要发送的交互，发送联邦必须首先发布它，这意味着它告诉每个人它有一些信息并且它想要共享它。联邦成员要接收某个类的交互，它必须订阅该交互类。这意味着发送的指定类的所有交互将仅传递给订阅联邦。

您可以使用以下代码订阅交互类 _messageId：

```java
_rtiAmbassador.subscribeInteractionClass(_messageId);
```

您现在将收到由其他联邦发布和发送的类 _messageId 的所有交互。

要发布相同的交互类，请使用以下代码：

```java
_rtiAmbassador.publishInteractionClass(_messageId);
```

其他联邦成员现在将收到您发送的交互（如果他们已订阅）。

## Sending Interactions

在发送交互之前，您必须创建一个 ParameterHandleValueMap 并添加交互的参数以及根据 FOM 编码的参数值。代码如下所示：

```java
ParameterHandleValueMap parameters;
parameters = _rtiAmbassador.getParameterHandleValueMapFactory().create(1);

HLAunicodeString messageEncoder = _encoderFactory.createHLAunicodeString();
HLAunicodeString nameEncoder = _encoderFactory.createHLAunicodeString();

messageEncoder.setValue(_message);
nameEncoder.setValue(_username);

parameters.put(_parameterIdText, messageEncoder.toByteArray());
parameters.put(_parameterIdSender, nameEncoder.toByteArray());
```

message 和 username 是您要发送的参数的值。 ParameterHandleValueMap 基于 java.util.Map 接口并使用 ParameterHandle 作为键和值作为值。

要发送交互，请进行以下调用：

```java
_rtiAmbassador.sendInteraction(_messageId, parameters, null);
```

第一个参数是交互类句柄，第二个是 ParameterHandleValueMap，保存交互的参数值，第三个是用户提供的标签，在这种情况下设置为 null。

## Receiving Interactions

当 RTI 想要向联邦发送数据时，它使用 FederateAmbassador 类中指定的方法（称为回调）。 FederateAmbassadorImpl 类中的方法都是空的，这意味着您只需实现您对子类感兴趣的回调。在聊天示例中，只实现了一个回调，即receiveInteraction，当另一个联邦发送的交互要传递给您的联邦时，RTI 会调用该回调。

receiveInteraction 回调如下所示：

```java
public void receiveInteraction(InteractionClassHandle interactionClass,
ParameterHandleValueMap theParameters,
byte[] userSuppliedTag,
OrderType sentOrdering,
TransportationTypeHandle theTransport,
SupplementalReceiveInfo receiveInfo)
```

第一个参数是接收到的交互的类，第二个是交互的参数。最后四个在这里不感兴趣。

例如，要从 ParameterHandleValueMap 中获取参数值，您可以使用这样的 for 循环遍历map：

```java
String message;
String sender;

for (Iterator i = theParameters.keySet().iterator(); i.hasNext(); ) {
    ParameterHandle parameterHandle = (ParameterHandle) i.next();
    if (parameterHandle.equals(_parameterIdText)) {
        HLAunicodeString messageDecoder =
        _encoderFactory.createHLAunicodeString();
        messageDecoder.decode((byte[]) theParameters.get(_parameterIdText));
        message = messageDecoder.getValue();
    } else if (parameterHandle.equals(_parameterIdSender)) {
        HLAunicodeString senderDecoder =
        _encoderFactory.createHLAunicodeString();
        senderDecoder.decode((byte[]) theParameters.get(_parameterIdSender));
        sender = senderDecoder.getValue();
    }
}
System.out.println(sender + ": "" + message);
```

对于map中的每个条目，您检查参数句柄。在本例中，参数值根据 FOM 进行解码。

