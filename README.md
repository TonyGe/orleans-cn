#Orleans：一种可编程、可伸缩的分布式参与者模式实现

##摘要

大规模的交互式服务需要同时满足高吞吐、低延时以及高可用三个特性。经典的三层无状态的系统架构很难同时满足上述的三个特性需求。参与者模式使得构建一个无状态的中间层以获得高性能变得更加自然。但是目前流行的参与者模式框架依然把不少的分布式系统中的问题交给了开发者去解决。

Orleans开发模型引入了一种虚拟的参与者，解决了一些分布式系统中常见复杂问题，包括可靠性和分布式资源管理，让开发者从这些问题的困境中得以解放。同时，Orleans运行时能够使得应用同时获得高性能、高可靠以及高可扩展性。

这篇文章将介绍Orleans能够用简单的编程模型实现这些目标背后的设计原则以示例。我们将描述如何使用Orleans来简化在Windows云上开发可扩展的生产应用的过程。并且总结使用Orleans之后这些系统的性能情况。

##介绍

构建可扩展并且可靠的交互式服务是困难的。由于直接影响到终端用户的体验，交互式系统的交互特性推服务的可用性和即时响应特性提出了很高的要求。为了自测大量的并发用户，系统必须实现高吞吐。

无状态前端、无状态中间层和存储层构成的经典三层结构，由于每一次请求都需要访问到存储层，整个系统的可扩展性会受限于存储层在系统延时和吞吐量方面的表现。通常，我们会在存储层和无状态中间层之间增加一层缓存以提升整个系统的性能。底层存储层的大部分并发性和语义保证因为缓存的引入而丢失。为了解决对于缓存中的数据进行并发写带来的数据不一致，整个应用或者缓存层需要额外的工作以保证一致性。因为使用了数据传输范例：对于每个请求，数据从存储或缓存发送到正在处理请求的中间层服务器，不管应用是否使用缓存，无状态的中间层都无法保证数据的局部性。