# Chap1 Real-Time is Now

## By 韩祝鹏

Real-Time 的网络应用是当下的需求，随着技术的发展，变得容易了。激动人心.

（安迪-比尔定律，多核、网络的发展，高并发实时连接的需求）

*用户希望应用能反应当前世界的状态。*

Users expect applications to reflect the corrent state of the world.

（整个应用，或整个公司，甚至整个网络，都应该是一个整体的。公司产品的各个相关方：用户-运营人员-管理者，都应该理论上能实时看到当前整个世界的状态，并相应作出立即的动作）

传统的实时技术（iframe）等，需要适配不同的浏览器。

## 实时 的定义

- 硬件，硬实时（飞机控制系统等）
- 软实时
- 不需要用户介入，更新当前的状态。

## The Layers of a Real-time system 实时系统的各个层

*Clients - 实时通讯层-后台服务器群（共同完成业务目标）*

### Client 客户端层
- 用户界面的控制
- 维持与服务器的连接
- 主要问题：网络连接的不稳定性

### Communication Layer 通讯层
	Persistent connection，维持长连接
	HTTP/2 ， Server-sent events and WebSockets
	本书使用 WebSocket
	客户端与服务器不要绑定在一种通讯层技术上，要解耦，适应未来可能的技术发展变化。

### Server 服务端
	Client 通过通讯层连接到“单一的”server上，连接尽可能保持。
	传统的web应用是短连接。
	实时系统与传统web应用，都是与服务器发request，不同点在于实时系统的请求是“有状态”的。
	Statefulness。

	HTTP的web request 是无状态的，服务器在请求之间不维护状态。客户端发每个HTTP request，必须发送状态，如cookie。
	实时系统的服务器，可以把状态与特定的连接相关联。（如用户和应用的数据）
	这样实时系统不需要在每次接到request的时候，都重新组装整个world的环境（如数据库等连接，涉及到的用户与应用的各种状态数据环境），这会减轻大量的工作，因此响应会快的多。（并且开发起来简单很多。）

	传统web，多个服务器是尽量隔离的，因为request无状态，这样可以容错，扩展。
	实时系统，需要各个server互相之间是连接在一起的，需要知道哪个用户连接在哪个server，并互相通信。

	维护多个实例的状态与行为的应用，就是分布式的应用。分布式应用有很多好处，也有很多挑战。

### Scalability 可扩展性
	可扩展性的类型：性能，可维护性，成本

#### 性能的可扩展性
	实时系统有些特有的扩展性问题，如大量的实时连接的服务器之间共享信息。

#### 维护性的可扩展性
	添加新特性，debug，持续运行。
	维护性差意味着添加新特性或改bug时，开发人员要花费更多的时间，而且很受挫。

	可维护性是“面向未来”的问题，因此很容易被忽视。
	通过清晰的分层，划分边界，最佳实践，可以提高未来的可维护性。

### 成本的可扩展性
	CPU、内存，带宽，未来的开发时间
	Erlang应用相对成本低，成功案例：WhatsApp

### 各个可扩展性之间关系矛盾，需要取舍。“fast，reliable，cheap--pick two”
#### 性能 vs 成本
		当新特性的开发优先级更高时，通过花钱砸服务器先解决
		也可以通过云服务器，动态伸缩服务器的数量。

#### 性能 vs 可维护性
		打破层之间的 边界，将通讯层与业务代码紧耦合，能提高性能，但会严重降低可维护性。
		应该最大化可维护性，这样新的特性容易后续添加。（牺牲一些性能，成本投入，等待未来硬件性能提升等）。这在商业上是更有利的选择。

		当性能改进的收益大过新特性的时候，在考虑。

#### 可维护性 vs 成本
		人很贵！
		不去还技术债，可以短期最小化成本，但长期成本增加。（而且是非线性的增加，早晚会到临界点，推倒重来会是唯一的选择）。

## 用Elixir 实现实时性
	Elixir语言较为灵活，表达能力强，降低代码的复杂度，因此降低维护成本。（代码简洁，可维护性好）
	Elixir构建在 Erlang/OTP 之上，利用虚拟机的轻量级进程，通常使用 GenServer，封装和建模实时系统中的各个模块。
	扩展Elixir应用，只要简单的将各个服务器连接起来，就得到了一个集群。
	（Actor模型，天然的分布式优势。在单机上纵向扩展，利用多核，或多机的横向分布扩展）

	任何的系统，尤其是实时系统，都需要数据和错误处理的隔离。
	Erlang历史上是为电话交换机开发设计的，天然需要数据的隔离，一个通话的崩溃不会影响其他的通话。
	用 Erlang 的 Process来分隔系统里的各个模块。

	Elixir是开发实时系统的狠好的选择，但需要深入理解 OTP的设计思想和最佳实践。


## Building Real-Time Systems
	Phoenix是Elixir语言开发Web框架。
	Phoenix Channel是构建实时系统的组建。
	其他的库： GenStage， Phoenix Tracker。后续章节介绍。

## 总结
	现代应用对实时性的需求，各种可扩展性及成本等的考虑，易用性，各种因素最终指向了 Elixir及 Phoenix。
