Title: 设计模式 Rust 表达-设计原则(SOLID)
Date: 2023-07-19 00:16
Modified: 2023-07-19 00:16
Category: Design Pattern
Tags: design pattern, rust
Slug: design-pattern-rust-1
Authors: Hacken
Summary: 复习设计原则 SOLID

### SOLID 具体指的是什么？

1. 单一职责，Single Responsibility Principle
2. 开放封闭, Open Closed Principle
3. 里式替换, Liskov Substitution Principle
4. 接口隔离, Interface Segregation Principle
5. 依赖倒置, Dependency Inversion Principle

这些原则独立的被列出来，但是他们并不是正交的，反而他们之间是相互依赖的，或者说是相互支撑的，这里的支撑是指如果没有其中的一个原则，那么其他的原则也就不会存在了。而他们都有一个共同的目标

> 编写可复用的可扩展的程序

这么简短的一句话，在你没有见过大量反面程序的时候，你是无法深刻理解的。什么事反面程序呢？就是你在维护一个程序的时候，你会发现你修改了一个地方，然后另外一个地方就出现了问题，然后你又去修改另外一个地方，然后又出现了问题，这样的修改是没有尽头的，而且你修改的地方越多，你的程序就越不稳定，越不可靠，越不可维护。

为什么没要接口隔离，我觉得这是对依赖倒置的一个深化或者说细化。在依赖倒置中强调依赖接口而不是具体实现来达到复用性和扩展性。这其实对接口的定义，或者说如何定义出好的接口提出了很高的要求，如果接口定义的不好，老是变化，那也没有达到目的。所以接口隔离原则就是来指导如何更好的定义接口。要求接口的定义要精简单一内聚性好，完备，不要定义过多的接口，而且接口的定义要稳定，不要老是变化。
