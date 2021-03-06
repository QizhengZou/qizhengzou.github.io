# Storage_cloud_primordial


# etcd实战
（kubernetes的元数据存储，保存了你通过kubectl命令所看到的node/pod/deployment等各种资源、配置信息等）
## 开篇词｜为什么你要学习etcd?
唐聪 2021-01-20
开门见山，今天我想和你聊聊为什么要学习 etcd。随着 Kubernetes 成为容器编排领域霸主，etcd 也越来越火热，越来越多的软件工程师使用 etcd 去解决各类业务场景中遇到的痛点。你知道吗？etcd 的 GitHub star 数已超过 34.2K，它的应用场景相当广泛，从服务发现到分布式锁，从配置存储到分布式协调等等。可以说，etcd 已经成为了云原生和分布式系统的存储基石。另外，etcd 作为最热门的云原生存储之一，在腾讯、阿里、Google、AWS、美团、字节跳动、拼多多、Shopee、明源云等公司都有大量的应用，覆盖的业务可不仅仅是 Kubernetes 相关的各类容器产品，更有视频、推荐、安全、游戏、存储、集群调度等核心业务。

### 我想为你解决哪些问题？
在工作和参与 etcd 社区贡献的过程中，我经常会收到各类问题咨询，同时自己也经历了各种问题。我相信你在使用 Kubernetes、etcd 的过程中，很可能也会遇到下面这些典型问题：

etcd Watch 机制能保证事件不丢吗？（原理类）哪些因素会导致你的集群 Leader 发生切换? （稳定性类）为什么基于 Raft 实现的 etcd 还可能会出现数据不一致？（一致性类）为什么你删除了大量数据，db 大小不减少？为何 etcd 社区建议 db 大小不要超过 8G？（db 大小类）为什么集群各节点磁盘 I/O 延时很低，写请求也会超时？（延时类）为什么你只存储了 1 个几百 KB 的 key/value， etcd 进程却可能耗费数 G 内存? （内存类）当你在一个 namespace 下创建了数万个 Pod/CRD 资源时，同时频繁通过标签去查询指定 Pod/CRD 资源时，APIServer 和 etcd 为什么扛不住?（最佳实践类）

当然，你在学习和使用 etcd、Kubernetes 过程中遇到的问题肯定远远不止这些，下面我用思维导图给你总结了更多类似问题，你可以对照自身的经历去看一下。

![img](https://static001.geekbang.org/resource/image/7e/54/7e05c744ba292cf26c39d69101200554.jpg?wh=5378*2217)

这门课就是为了帮助你解决这些问题而生。不过你可能会想，你能把这些东西都讲明白么？我先和你聊聊我的个人 etcd 经历，你就知道我为什么有自信能带你学好 etcd 了。

### 我和 etcd 的那些事
本科毕业后，我通过校招加入了腾讯。不到一年的时间，我就主导完成了一个亿级用户的业务核心存储平滑迁移任务。

在 2015 到 2017 的这两年时间里，为了满足业务大量的 Redis 诉求，我基于 Redis/Codis 构建了大规模的排行榜和 Redis 集群平台服务，支撑了公司的多个重要业务。在这期间，我积累了大量的 NoSQL 数据库知识与经验，为后面工作转岗到 To B，负责 Kubernetes 的元数据存储 etcd 奠定了良好的基础。

2017 年后，我就开始接触 Docker 和 Kubernetes，并通过 Kubernetes 来解决大规模 Redis 集群的治理问题，提升服务的可用性、降低运维成本。

2018 年，我转岗到了腾讯云，负责 Kubernetes 集群存储 etcd 治理工作。我主导构建的云原生 etcd 平台，支持自动化的集群管理、调度、迁移、监控、巡检、备份，成功解决了集群大规模增长过程中的各类 etcd 稳定性问题，支撑了万级的 Kubernetes 和 etcd 集群。

etcd 平台从解决 Kubernetes etcd 稳定性问题，到为各类云原生产品提供 etcd 基础服务，再到保障开箱即用的腾讯云 etcd 产品化服务，它发挥着重要作用。在这个过程中，我也见证了越来越多的软件工程师加入 etcd 的阵营，越来越多的产品使用 etcd。目前，etcd 作为腾讯众多产品的基础设施，服务用户已达数亿。

同时，我也遇到了很多问题，从内存泄露到数据不一致，从节点 crash 到性能慢，再到死锁、OOM 等稳定性问题等等。最令我记忆犹新的是，我和小伙伴王超凡通过混沌工程发现并修复了多个数据不一致 Bug，其中一个 Bug 已经存在近 3 年之久，而且很严重，重启就可能会触发数据不一致。

从解决类似上面的棘手 Bug 到提交稳定性、性能优化 PR，从提交 QoS 特性设计方案、POC 到给新的 contributor review PR，通过一点点的积累，大量周末、凌晨时间的付出，我成为了 2020 年 etcd 社区的全球 Top3 活跃贡献者，与 Google、AWS、阿里巴巴的小伙伴们，一起推动 etcd 项目越来越好，服务于全球开发者。

总结来说的话，过去几年我一直在与 Redis、etcd 打交道，一线的经历、解决的问题都让我收获良多，所以我也非常有自信能把这些经验都交付给你。

在业务实践方面，我成功解决过众多大规模业务增长过程中，遇到的存储稳定性、可扩展性等痛点，积累了丰富的理论知识、大规模集群的实战、治理经验，能直接帮助到你今后的工作。另外，在 etcd 开源项目方面，我深度参与 etcd 开源项目的贡献经历，让我可以从开发者的视角，为你分析问题、梳理最佳实践、解读特性设计方案、阐述社区未来演进方向等等，帮助你深度理解 etcd 以及分布式服务。


### 你应该怎么学 etcd？
在我看来，etcd 学习其实可以分为大中小三个目标。最大的目标我当然是希望你能够用最低的学习成本，掌握 etcd 核心原理与最佳实践，让 etcd 为你所用，帮助你解决业务过程中的各类痛点，在工作中少踩坑、少交学费，多升职、多涨薪。

但是这个大的目标怎么实现呢？我的答案是使用拆解法。下面我给你提出了学习这个专栏的一些中等大小目标，希望你能带着这些目标进行学习，每过一段时间，回过头来看看，这些目标实现了多少？

首先，你能知道什么是 etcd，了解它的基本读写原理、核心特性和能解决什么问题。然后，在使用 etcd 解决各类业务场景需求时，能独立判断 etcd 是否适合你的业务场景，并能设计出良好的存储结构，避免 expensive request。其次，在使用 Kubernetes 的过程中，你能清晰地知道你的每个操作背后的 etcd 是如何工作的，并遵循 Kubernetes/etcd 最佳实践，让你的 Kubernetes 集群跑得更快更稳。接着，在运维 etcd 集群的时候，你能知道 etcd 集群核心监控指标，了解常见的坑，制定良好的巡检、监控策略，及时发现、规避问题，避免事故的产生。最后，当你遇到 etcd 问题时，能自己分析为什么会出现这样的错误，并知道如何解决，甚至给社区提 PR 优化，做到知其然知其所以然。

做到以上五个目标其实也并不容易，别着急，我们接着往下拆分。为了让你实现以上五个目标，我把专栏分为了基础和实践两大主线。每个主线里都有一个一个的小目标，我们逐个攻破就容易多了。

基础篇主线是为了帮助你建立起对 etcd 的整体认知，搞懂读写请求、各个核心特性背后的原理，为我们后面的实践篇打下基础。基础篇的学习也是一个中小型分布式存储系统从 0 到 1 的实现案例解读，学习它你收获的不仅仅是 etcd，更是如何构建分布式存储系统的理论知识。


我把基础篇分为了以下的学习小目标：etcd 基础架构。通过为你梳理 etcd 前世今生、分析 etcd 读写流程，帮助你建立起对 etcd 的整体认知，了解一个分布式存储系统的基本模型、设计思想。Raft 算法。通过为你介绍 Raft 算法在 etcd 中是如何工作的，帮助你了解 etcd 高可用、高可靠背后的核心原理。鉴权模块。通过介绍 etcd 的鉴权、授权体系，带你了解 etcd 是如何保护你的数据安全，以及各个鉴权机制的优缺点。租约模块。介绍 etcd 租约特性的实现，帮助你搞懂如何检测一个进程的存活性，为什么它可以用于 Leader 选举中。MVCC/Watch 模块。通过这两个模块帮助你搞懂 Kubernetes 控制器编程模型背后的原理。

在介绍 etcd 原理的过程中，我也会从更上层的角度，为你解读分布式系统存储系统的核心技术难点是什么，常见的解决方案有哪些，以及为什么 etcd 要这样设计、实现。让你对整个分布式系统有更深层次的理解，明白不同存储系统只是在面对各自的业务场景的时候，选择了合适的技术方案，让你从本质上去理解分布式存储系统要解决的核心问题基本是一致的。当然基础篇讲的远不止这些，关于基础篇的更多内容，你可以参考下面的 etcd 基础篇思维导图：

![img](https://static001.geekbang.org/resource/image/ed/ea/edf53f37c0725c9757e4ecb89982a7ea.jpg?wh=3920*2495)


通过基础篇掌握好 etcd 核心模块原理后，实践篇我将为你解读实际使用 etcd 时，可能会遇到的各种问题，帮助你提前避坑、遇到类似问题时能独立分析、解决。我把实践篇分为以下的学习小目标：

问题篇。为你分析 etcd 使用过程中的各类典型问题，和你细聊各种异常现象背后的原理、最佳实践。性能优化篇。通过读写链路的分析，为你梳理可能影响 etcd 性能的每一个瓶颈。实战篇。带你从 0 到 1 亲手参与构建一个简易的分布式 KV 数据库，进一步提升你对分布式存储系统的认知。Kubernetes 实践篇。为你分析 etcd 在 Kubernetes 中的应用，让你对 Kubernetes 原理有更深层次的理解。etcd 应用篇。介绍 etcd 在分布式锁、配置系统、服务发现场景中的应用。

更多实践篇内容你可以参考下面的思维导图：

![img](https://static001.geekbang.org/resource/image/42/81/42eaa94b0f4f7a18895780e6f61ce381.jpg?wh=3912*2990)

这样一来，我们的学习目标就比较明确了。最终目标是让 etcd 为你所用，少踩坑、多升职加薪；而为了实现这个目标，我们需要从多方面提升自己对 etcd 的掌控能力，也就是实现中等目标；但进阶的难度还是比较大的，所以我们需要把一个个小目标当作基石（也就是每一节课的知识点学习），来达成个人能力的提升。


## 基础

## 01 | etcd的前世今生：为什么Kubernetes使用etcd？

今天是专栏课程的第一讲，我们就从 etcd 的前世今生讲起。让我们一起穿越回 2013 年，看看 etcd 最初是在什么业务场景下被设计出来的？2013 年，有一个叫 CoreOS 的创业团队，他们构建了一个产品，Container Linux，它是一个开源、轻量级的操作系统，侧重自动化、快速部署应用服务，并要求应用程序都在容器中运行，同时提供集群化的管理方案，用户管理服务就像单机一样方便。他们希望在重启任意一节点的时候，用户的服务不会因此而宕机，导致无法提供服务，因此需要运行多个副本。但是多个副本之间如何协调，如何避免变更的时候所有副本不可用呢？为了解决这个问题，CoreOS 团队需要一个协调服务来存储服务配置信息、提供分布式锁等能力。怎么办呢？当然是分析业务场景、痛点、核心目标，然后是基于目标进行方案选型，评估是选择社区开源方案还是自己造轮子。这其实就是我们遇到棘手问题时的通用解决思路，CoreOS 团队同样如此。假设你是 CoreOS 团队成员，你认为在这样的业务场景下，理想中的解决方案应满足哪些目标呢？

如果你有过一些开发经验，应该能想到一些关键点了，我根据自己的经验来总结一下，一个协调服务，理想状态下大概需要满足以下五个目标：

可用性角度：高可用。协调服务作为集群的控制面存储，它保存了各个服务的部署、运行信息。若它故障，可能会导致集群无法变更、服务副本数无法协调。业务服务若此时出现故障，无法创建新的副本，可能会影响用户数据面。

数据一致性角度：提供读取“最新”数据的机制。既然协调服务必须具备高可用的目标，就必然不能存在单点故障（single point of failure），而多节点又引入了新的问题，即多个节点之间的数据一致性如何保障？比如一个集群 3 个节点 A、B、C，从节点 A、B 获取服务镜像版本是新的，但节点 C 因为磁盘 I/O 异常导致数据更新缓慢，若控制端通过 C 节点获取数据，那么可能会导致读取到过期数据，服务镜像无法及时更新。

容量角度：低容量、仅存储关键元数据配置。协调服务保存的仅仅是服务、节点的配置信息（属于控制面配置），而不是与用户相关的数据。所以存储上不需要考虑数据分片，无需过度设计。

功能：增删改查，监听数据变化的机制。协调服务保存了服务的状态信息，若服务有变更或异常，相比控制端定时去轮询检查一个个服务状态，若能快速推送变更事件给控制端，则可提升服务可用性、减少协调服务不必要的性能开销。

运维复杂度：可维护性。在分布式系统中往往会遇到硬件 Bug、软件 Bug、人为操作错误导致节点宕机，以及新增、替换节点等运维场景，都需要对协调服务成员进行变更。若能提供 API 实现平滑地变更成员节点信息，就可以大大降低运维复杂度，减少运维成本，同时可避免因人工变更不规范可能导致的服务异常。

了解完理想中的解决方案目标，我们再来看 CoreOS 团队当时为什么选择了从 0 到 1 开发一个新的协调服务呢？如果使用开源软件，当时其实是有 ZooKeeper 的，但是他们为什么不用 ZooKeeper 呢？我们来分析一下。从高可用性、数据一致性、功能这三个角度来说，ZooKeeper 是满足 CoreOS 诉求的。然而当时的 ZooKeeper 不支持通过 API 安全地变更成员，需要人工修改一个个节点的配置，并重启进程。若变更姿势不正确，则有可能出现脑裂等严重故障。适配云环境、可平滑调整集群规模、在线变更运行时配置是 CoreOS 的期望目标，而 ZooKeeper 在这块的可维护成本相对较高。


其次 ZooKeeper 是用 Java 编写的，部署较繁琐，占用较多的内存资源，同时 ZooKeeper RPC 的序列化机制用的是 Jute，自己实现的 RPC API。无法使用 curl 之类的常用工具与之互动，CoreOS 期望使用比较简单的 HTTP + JSON。

因此，CoreOS 决定自己造轮子，那 CoreOS 团队是如何根据系统目标进行技术方案选型的呢？

### etcd v1 和 v2 诞生
首先我们来看服务高可用及数据一致性。前面我们提到单副本存在单点故障，而多副本又引入数据一致性问题。因此为了解决数据一致性问题，需要引入一个共识算法，确保各节点数据一致性，并可容忍一定节点故障。常见的共识算法有 Paxos、ZAB、Raft 等。CoreOS 团队选择了易理解实现的 Raft 算法，它将复杂的一致性问题分解成 Leader 选举、日志同步、安全性三个相对独立的子问题，只要集群一半以上节点存活就可提供服务，具备良好的可用性。其次我们再来看数据模型（Data Model）和 API。数据模型参考了 ZooKeeper，使用的是基于目录的层次模式。API 相比 ZooKeeper 来说，使用了简单、易用的 REST API，提供了常用的 Get/Set/Delete/Watch 等 API，实现对 key-value 数据的查询、更新、删除、监听等操作。key-value 存储引擎上，ZooKeeper 使用的是 Concurrent HashMap，而 etcd 使用的是则是简单内存树，它的节点数据结构精简后如下，含节点路径、值、孩子节点信息。这是一个典型的低容量设计，数据全放在内存，无需考虑数据分片，只能保存 key 的最新版本，简单易实现。

![img](https://static001.geekbang.org/resource/image/ff/41/ff4ee032739b9b170af1b2e2ba530e41.png?wh=1024*904)



```
type node struct {
   Path string  //节点路径
   Parent *node //关联父亲节点
   Value      string     //key的value值
   ExpireTime time.Time //过期时间
   Children   map[string]*node //此节点的孩子节点
}
```
最后我们再来看可维护性。Raft 算法提供了成员变更算法，可基于此实现成员在线、安全变更，同时此协调服务使用 Go 语言编写，无依赖，部署简单。

![img](https://static001.geekbang.org/resource/image/dd/70/dd253e4fc19885fa6f00c278762ba270.png?wh=1920*1402)



基于以上技术方案和架构图，CoreOS 团队在 2013 年 8 月对外发布了第一个测试版本 v0.1，API v1 版本，命名为 etcd。那么 etcd 这个名字是怎么来的呢？其实它源于两个方面，unix 的“/etc”文件夹和分布式系统 (“D”istribute system) 的 D，组合在一起表示 etcd 是用于存储分布式配置的信息存储服务。v0.1 版本实现了简单的 HTTP Get/Set/Delete/Watch API，但读数据一致性无法保证。v0.2 版本，支持通过指定 consistent 模式，从 Leader 读取数据，并将 Test And Set 机制修正为 CAS(Compare And Swap)，解决原子更新的问题，同时发布了新的 API 版本 v2，这就是大家熟悉的 etcd v2 版本，第一个非 stable 版本。下面，我用一幅时间轴图，给你总结一下 etcd v1/v2 关键特性。

![img](https://static001.geekbang.org/resource/image/d0/0e/d0af3537c0eef89b499a82693da23f0e.png?wh=1920*727)



### 为什么 Kubernetes 使用 etcd?
这张图里，我特别标注出了 Kubernetes 的发布时间点，这个非常关键。我们必须先来说说这个事儿，也就是 Kubernetes 和 etcd 的故事。2014 年 6 月，Google 的 Kubernetes 项目诞生了，我们前面所讨论到 Go 语言编写、etcd 高可用、Watch 机制、CAS、TTL 等特性正是 Kubernetes 所需要的，它早期的 0.4 版本，使用的正是 etcd v0.2 版本。

Kubernetes 是如何使用 etcd v2 这些特性的呢？举几个简单小例子。

当你使用 Kubernetes 声明式 API 部署服务的时候，Kubernetes 的控制器通过 etcd Watch 机制，会实时监听资源变化事件，对比实际状态与期望状态是否一致，并采取协调动作使其一致。Kubernetes 更新数据的时候，通过 CAS 机制保证并发场景下的原子更新，并通过对 key 设置 TTL 来存储 Event 事件，提升 Kubernetes 集群的可观测性，基于 TTL 特性，Event 事件 key 到期后可自动删除。

Kubernetes 项目使用 etcd，除了技术因素也与当时的商业竞争有关。CoreOS 是 Kubernetes 容器生态圈的核心成员之一。

当时 Docker 容器浪潮正席卷整个开源技术社区，CoreOS 也将容器集成到自家产品中。一开始与 Docker 公司还是合作伙伴，然而 Docker 公司不断强化 Docker 的 PaaS 平台能力，强势控制 Docker 社区，这与 CoreOS 核心商业战略出现了冲突，也损害了 Google、RedHat 等厂商的利益。

最终 CoreOS 与 Docker 分道扬镳，并推出了 rkt 项目来对抗 Docker，然而此时 Docker 已深入人心，CoreOS 被 Docker 全面压制。

以 Google、RedHat 为首的阵营，基于 Google 多年的大规模容器管理系统 Borg 经验，结合社区的建议和实践，构建以 Kubernetes 为核心的容器生态圈。相比 Docker 的垄断、独裁，Kubernetes 社区推行的是民主、开放原则，Kubernetes 每一层都可以通过插件化扩展，在 Google、RedHat 的带领下不断发展壮大，etcd 也进入了快速发展期。

在 2015 年 1 月，CoreOS 发布了 etcd 第一个稳定版本 2.0，支持了 quorum read，提供了严格的线性一致性读能力。7 月，基于 etcd 2.0 的 Kubernetes 第一个生产环境可用版本 v1.0.1 发布了，Kubernetes 开始了新的里程碑的发展。

etcd v2 在社区获得了广泛关注，GitHub star 数在 2015 年 6 月就高达 6000+，超过 500 个项目使用，被广泛应用于配置存储、服务发现、主备选举等场景。

下图我从构建分布式系统的核心要素角度，给你总结了 etcd v2 核心技术点。无论是 NoSQL 存储还是 SQL 存储、文档存储，其实大家要解决的问题都是类似的，基本就是图中总结的数据模型、复制、共识算法、API、事务、一致性、成员故障检测等方面。希望通过此图帮助你了解从 0 到 1 如何构建、学习一个分布式系统，要解决哪些技术点，在心中有个初步认识，后面的课程中我会再深入介绍。

![img](https://static001.geekbang.org/resource/image/cd/f0/cde3f155f51bfd3d7fd78fe8e7ac9bf0.png?wh=1920*943)


### etcd v3 诞生
然而随着 Kubernetes 项目不断发展，v2 版本的瓶颈和缺陷逐渐暴露，遇到了若干性能和稳定性问题，Kubernetes 社区呼吁支持新的存储、批评 etcd 不可靠的声音开始不断出现。具体有哪些问题呢？我给你总结了如下图：

![img](https://static001.geekbang.org/resource/image/88/d1/881db1b7d05dc40771e9737f3117f5d1.png?wh=1920*577)

下面我分别从功能局限性、Watch 事件的可靠性、性能、内存开销来分别给你剖析 etcd v2 的问题。

首先是功能局限性问题。它主要是指 etcd v2 不支持范围和分页查询、不支持多 key 事务。

第一，etcd v2 不支持范围查询和分页。分页对于数据较多的场景是必不可少的。在 Kubernetes 中，在集群规模增大后，Pod、Event 等资源可能会出现数千个以上，但是 etcd v2 不支持分页，不支持范围查询，大包等 expensive request 会导致严重的性能乃至雪崩问题。第二，etcd v2 不支持多 key 事务。在实际转账等业务场景中，往往我们需要在一个事务中同时更新多个 key。

然后是 Watch 机制可靠性问题。Kubernetes 项目严重依赖 etcd Watch 机制，然而 etcd v2 是内存型、不支持保存 key 历史版本的数据库，只在内存中使用滑动窗口保存了最近的 1000 条变更事件，当 etcd server 写请求较多、网络波动时等场景，很容易出现事件丢失问题，进而又触发 client 数据全量拉取，产生大量 expensive request，甚至导致 etcd 雪崩。

其次是性能瓶颈问题。etcd v2 早期使用了简单、易调试的 HTTP/1.x API，但是随着 Kubernetes 支撑的集群规模越来越大，HTTP/1.x 协议的瓶颈逐渐暴露出来。比如集群规模大时，由于 HTTP/1.x 协议没有压缩机制，批量拉取较多 Pod 时容易导致 APIServer 和 etcd 出现 CPU 高负载、OOM、丢包等问题。

另一方面，etcd v2 client 会通过 HTTP 长连接轮询 Watch 事件，当 watcher 较多的时候，因 HTTP/1.x 不支持多路复用，会创建大量的连接，消耗 server 端过多的 socket 和内存资源。

同时 etcd v2 支持为每个 key 设置 TTL 过期时间，client 为了防止 key 的 TTL 过期后被删除，需要周期性刷新 key 的 TTL。

实际业务中很有可能若干 key 拥有相同的 TTL，可是在 etcd v2 中，即使大量 key TTL 一样，你也需要分别为每个 key 发起续期操作，当 key 较多的时候，这会显著增加集群负载、导致集群性能显著下降。

最后是**内存开销问题**。etcd v2 在内存维护了一颗树来保存所有节点 key 及 value。在数据量场景略大的场景，如配置项较多、存储了大量 Kubernetes Events， 它会导致较大的内存开销，同时 etcd 需要定时把全量内存树持久化到磁盘。这会消耗大量的 CPU 和磁盘 I/O 资源，对系统的稳定性造成一定影响。

为什么 etcd v2 有以上若干问题，Consul 等其他竞品依然没有被 Kubernetes 支持呢？

一方面当时包括 Consul 在内，没有一个开源项目是十全十美完全满足 Kubernetes 需求。而 CoreOS 团队一直在聆听社区的声音并积极改进，解决社区的痛点。用户吐槽 etcd 不稳定，他们就设计实现自动化的测试方案，模拟、注入各类故障场景，及时发现修复 Bug，以提升 etcd 稳定性。另一方面，用户吐槽性能问题，针对 etcd v2 各种先天性缺陷问题，他们从 2015 年就开始设计、实现新一代 etcd v3 方案去解决以上痛点，并积极参与 Kubernetes 项目，负责 etcd v2 到 v3 的存储引擎切换，推动 Kubernetes 项目的前进。同时，设计开发通用压测工具、输出 Consul、ZooKeeper、etcd 性能测试报告，证明 etcd 的优越性。

etcd v3 就是为了解决以上稳定性、扩展性、性能问题而诞生的。

在内存开销、Watch 事件可靠性、功能局限上，它通过引入 B-tree、boltdb 实现一个 MVCC 数据库，数据模型从层次型目录结构改成扁平的 key-value，提供稳定可靠的事件通知，实现了事务，支持多 key 原子更新，同时基于 boltdb 的持久化存储，显著降低了 etcd 的内存占用、避免了 etcd v2 定期生成快照时的昂贵的资源开销。


性能上，首先 etcd v3 使用了 gRPC API，使用 protobuf 定义消息，消息编解码性能相比 JSON 超过 2 倍以上，并通过 HTTP/2.0 多路复用机制，减少了大量 watcher 等场景下的连接数。其次使用 Lease 优化 TTL 机制，每个 Lease 具有一个 TTL，相同的 TTL 的 key 关联一个 Lease，Lease 过期的时候自动删除相关联的所有 key，不再需要为每个 key 单独续期。最后是 etcd v3 支持范围、分页查询，可避免大包等 expensive request。


2016 年 6 月，etcd 3.0 诞生，随后 Kubernetes 1.6 发布，默认启用 etcd v3，助力 Kubernetes 支撑 5000 节点集群规模。下面的时间轴图，我给你总结了 etcd3 重要特性及版本发布时间。从图中你可以看出，从 3.0 到未来的 3.5，更稳、更快是 etcd 的追求目标。

![img](https://static001.geekbang.org/resource/image/5f/6d/5f1bf807db06233ed51d142917798b6d.png?wh=1920*826)

从 2013 年发布第一个版本 v0.1 到今天的 3.5.0-pre，从 v2 到 v3，etcd 走过了 7 年的历程，etcd 的稳定性、扩展性、性能不断提升。发展到今天，在 GitHub 上 star 数超过 34K。在 Kubernetes 的业务场景磨炼下它不断成长，走向稳定和成熟，成为技术圈众所周知的开源产品，而 **v3 方案的发布，也标志着 etcd 进入了技术成熟期，成为云原生时代的首选元数据存储产品。**

### 小结
最后我们来小结下今天的内容，我们从如下几个方面介绍了 etcd 的前世今生，并在过程中详细解读了为什么 Kubernetes 使用 etcd：

etcd 诞生背景， etcd v2 源自 CoreOS 团队遇到的服务协调问题。etcd 目标，我们通过实际业务场景分析，得到理想中的协调服务核心目标：高可用、数据一致性、Watch、良好的可维护性等。而在 CoreOS 团队看来，高可用、可维护性、适配云、简单的 API、良好的性能对他们而言是非常重要的，ZooKeeper 无法满足所有诉求，因此决定自己构建一个分布式存储服务。介绍了 v2 基于目录的层级数据模型和 API，并从分布式系统的角度给你详细总结了 etcd v2 技术点。etcd 的高可用、Watch 机制与 Kubernetes 期望中的元数据存储是匹配的。etcd v2 在 Kubernetes 的带动下，获得了广泛的应用，但也出现若干性能和稳定性、功能不足问题，无法满足 Kubernetes 项目发展的需求。CoreOS 团队未雨绸缪，从问题萌芽时期就开始构建下一代 etcd v3 存储模型，分别从性能、稳定性、功能上等成功解决了 Kubernetes 发展过程中遇到的瓶颈，也捍卫住了作为 Kubernetes 存储组件的地位。

希望通过今天的介绍， 让你对 etcd 为什么有 v2 和 v3 两个大版本，etcd 如何从 HTTP/1.x API 到 gRPC API、单版本数据库到多版本数据库、内存树到 boltdb、TTL 到 Lease、单 key 原子更新到支持多 key 事务的演进过程有个清晰了解。希望你能有所收获，在后续的课程中我会和你深入讨论各个模块的细节。


## 02 | 基础架构：etcd一个读请求是如何执行的？

在上一讲中，我和你分享了 etcd 的前世今生，同时也为你重点介绍了 etcd  v2 的不足之处，以及我们现在广泛使用 etcd v3 的原因。今天，我想跟你介绍一下 etcd v3 的基础架构，让你从整体上对 etcd 有一个初步的了解，心中能构筑起一幅 etcd 模块全景图。这样，在你遇到诸如“Kubernetes 在执行 kubectl get pod 时，etcd 如何获取到最新的数据返回给 APIServer？”等流程架构问题时，就能知道各个模块由上至下是如何紧密协作的。即便是遇到请求报错，你也能通过顶层的模块全景图，推测出请求流程究竟在什么模块出现了问题。


### 基础架构
下面是一张 etcd 的简要基础架构图，我们先从宏观上了解一下 etcd 都有哪些功能模块。

![img](https://static001.geekbang.org/resource/image/34/84/34486534722d2748d8cd1172bfe63084.png?wh=1920*1240)

你可以看到，按照分层模型，etcd 可分为 Client 层、API 网络层、Raft 算法层、逻辑层和存储层。这些层的功能如下：

Client 层：Client 层包括 client v2 和 v3 两个大版本 API 客户端库，提供了简洁易用的 API，同时支持负载均衡、节点间故障自动转移，可极大降低业务使用 etcd 复杂度，提升开发效率、服务可用性。API 网络层：API 网络层主要包括 client 访问 server 和 server 节点之间的通信协议。一方面，client 访问 etcd server 的 API 分为 v2 和 v3 两个大版本。v2 API 使用 HTTP/1.x 协议，v3 API 使用 gRPC 协议。同时 v3 通过 etcd grpc-gateway 组件也支持 HTTP/1.x 协议，便于各种语言的服务调用。另一方面，server 之间通信协议，是指节点间通过 Raft 算法实现数据复制和 Leader 选举等功能时使用的 HTTP 协议。Raft 算法层：Raft 算法层实现了 Leader 选举、日志复制、ReadIndex 等核心算法特性，用于保障 etcd 多个节点间的数据一致性、提升服务可用性等，是 etcd 的基石和亮点。功能逻辑层：etcd 核心特性实现层，如典型的 KVServer 模块、MVCC 模块、Auth 鉴权模块、Lease 租约模块、Compactor 压缩模块等，其中 MVCC 模块主要由 treeIndex 模块和 boltdb 模块组成。存储层：存储层包含预写日志 (WAL) 模块、快照 (Snapshot) 模块、boltdb 模块。其中 WAL 可保障 etcd crash 后数据不丢失，boltdb 则保存了集群元数据和用户写入的数据。

etcd 是典型的读多写少存储，在我们实际业务场景中，读一般占据 2/3 以上的请求。为了让你对 etcd 有一个深入的理解，接下来我会分析一个读请求是如何执行的，带你了解 etcd 的核心模块，进而由点及线、由线到面地帮助你构建 etcd 的全景知识脉络。在下面这张架构图中，我用序号标识了 etcd 默认读模式（线性读）的执行流程，接下来，我们就按照这个执行流程从头开始说。

![img](https://static001.geekbang.org/resource/image/45/bb/457db2c506135d5d29a93ef0bd97e4bb.png?wh=1920*1229)


### 环境准备
首先介绍一个好用的进程管理工具[goreman](https://github.com/mattn/goreman)，基于它，我们可快速创建、停止本地的多节点 etcd 集群。你可以通过如下go get命令快速安装 goreman，然后从[etcd release](https://github.com/etcd-io/etcd/releases/v3.4.9)页下载 etcd v3.4.9 二进制文件，再从[etcd 源码](https://github.com/etcd-io/etcd/blob/v3.4.9/Procfile)中下载 goreman Procfile 文件，它描述了 etcd 进程名、节点数、参数等信息。最后通过goreman -f Procfile start命令就可以快速启动一个 3 节点的本地集群了。

```
go get github.com/mattn/goreman
```
### client
启动完 etcd 集群后，当你用 etcd 的客户端工具 etcdctl 执行一个 get hello 命令（如下）时，对应到图中流程一，etcdctl 是如何工作的呢？

```
etcdctl get hello --endpoints http://127.0.0.1:2379  
hello  
world  
```
首先，etcdctl 会对命令中的参数进行解析。我们来看下这些参数的含义，其中，参数“get”是请求的方法，它是 KVServer 模块的 API；“hello”是我们查询的 key 名；“endpoints”是我们后端的 etcd 地址，通常，生产环境下中需要配置多个 endpoints，这样在 etcd 节点出现故障后，client 就可以自动重连到其它正常的节点，从而保证请求的正常执行。在 etcd v3.4.9 版本中，etcdctl 是通过 clientv3 库来访问 etcd server 的，clientv3 库基于 gRPC client API 封装了操作 etcd KVServer、Cluster、Auth、Lease、Watch 等模块的 API，同时还包含了负载均衡、健康探测和故障切换等特性。在解析完请求中的参数后，etcdctl 会创建一个 clientv3 库对象，使用 KVServer 模块的 API 来访问 etcd server。接下来，就需要为这个 get hello 请求选择一个合适的 etcd server 节点了，这里得用到负载均衡算法。在 etcd 3.4 中，clientv3 库采用的负载均衡算法为 Round-robin。针对每一个请求，Round-robin 算法通过轮询的方式依次从 endpoint 列表中选择一个 endpoint 访问 (长连接)，使 etcd server 负载尽量均衡。关于负载均衡算法，你需要特别注意以下两点。

如果你的 client 版本 <= 3.3，那么当你配置多个 endpoint 时，负载均衡算法仅会从中选择一个 IP 并创建一个连接（Pinned endpoint），这样可以节省服务器总连接数。但在这我要给你一个小提醒，在 heavy usage 场景，这可能会造成 server 负载不均衡。在 client 3.4 之前的版本中，负载均衡算法有一个严重的 Bug：如果第一个节点异常了，可能会导致你的 client 访问 etcd server 异常，特别是在 Kubernetes 场景中会导致 APIServer 不可用。不过，该 Bug 已在 Kubernetes 1.16 版本后被修复。

为请求选择好 etcd server 节点，client 就可调用 etcd server 的 KVServer 模块的 Range RPC 方法，把请求发送给 etcd server。这里我说明一点，client 和 server 之间的通信，使用的是基于 HTTP/2 的 gRPC 协议。相比 etcd v2 的 HTTP/1.x，HTTP/2 是基于二进制而不是文本、支持多路复用而不再有序且阻塞、支持数据压缩以减少包大小、支持 server push 等特性。因此，基于 HTTP/2 的 gRPC 协议具有低延迟、高性能的特点，有效解决了我们在上一讲中提到的 etcd v2 中 HTTP/1.x 性能问题。

### KVServer
client 发送 Range RPC 请求到了 server 后，就开始进入我们架构图中的流程二，也就是 KVServer 模块了。etcd 提供了丰富的 metrics、日志、请求行为检查等机制，可记录所有请求的执行耗时及错误码、来源 IP 等，也可控制请求是否允许通过，比如 etcd Learner 节点只允许指定接口和参数的访问，帮助大家定位问题、提高服务可观测性等，而这些特性是怎么非侵入式的实现呢？答案就是拦截器。


#### 拦截器
etcd server 定义了如下的 Service KV 和 Range 方法，启动的时候它会将实现 KV 各方法的对象注册到 gRPC Server，并在其上注册对应的拦截器。下面的代码中的 Range 接口就是负责读取 etcd key-value 的的 RPC 接口。

```
service KV {  
  // Range gets the keys in the range from the key-value store.  
  rpc Range(RangeRequest) returns (RangeResponse) {  
      option (google.api.http) = {  
        post: "/v3/kv/range"  
        body: "*"  
      };  
  }  
  ....
}  
```
拦截器提供了在执行一个请求前后的 hook 能力，除了我们上面提到的 debug 日志、metrics 统计、对 etcd Learner 节点请求接口和参数限制等能力，etcd 还基于它实现了以下特性:要求执行一个操作前集群必须有 Leader；请求延时超过指定阈值的，打印包含来源 IP 的慢查询日志 (3.5 版本)。


server 收到 client 的 Range RPC 请求后，根据 ServiceName 和 RPC Method 将请求转发到对应的 handler 实现，handler 首先会将上面描述的一系列拦截器串联成一个执行，在拦截器逻辑中，通过调用 KVServer 模块的 Range 接口获取数据。


### 串行读与线性读
进入 KVServer 模块后，我们就进入核心的读流程了，对应架构图中的流程三和四。我们知道 etcd 为了保证服务高可用，生产环境一般部署多个节点，那各个节点数据在任意时间点读出来都是一致的吗？什么情况下会读到旧数据呢？这里为了帮助你更好的理解读流程，我先简单提下写流程。如下图所示，当 client 发起一个更新 hello 为 world 请求后，若 Leader 收到写请求，它会将此请求持久化到 WAL 日志，并广播给各个节点，若一半以上节点持久化成功，则该请求对应的日志条目被标识为已提交，etcdserver 模块异步从 Raft 模块获取已提交的日志条目，应用到状态机 (boltdb 等)。

![img](https://static001.geekbang.org/resource/image/cf/d5/cffba70a79609f29e1f2ae1f3bd07fd5.png?wh=1920*1074)

此时若 client 发起一个读取 hello 的请求，假设此请求直接从状态机中读取， 如果连接到的是 C 节点，若 C 节点磁盘 I/O 出现波动，可能导致它应用已提交的日志条目很慢，则会出现更新 hello 为 world 的写命令，在 client 读 hello 的时候还未被提交到状态机，因此就可能读取到旧数据，如上图查询 hello 流程所示。从以上介绍我们可以看出，在多节点 etcd 集群中，各个节点的状态机数据一致性存在差异。而我们不同业务场景的读请求对数据是否最新的容忍度是不一样的，有的场景它可以容忍数据落后几秒甚至几分钟，有的场景要求必须读到反映集群共识的最新数据。

我们首先来看一个对数据敏感度较低的场景。

假如老板让你做一个旁路数据统计服务，希望你每分钟统计下 etcd 里的服务、配置信息等，这种场景其实对数据时效性要求并不高，读请求可直接从节点的状态机获取数据。即便数据落后一点，也不影响业务，毕竟这是一个定时统计的旁路服务而已。这种直接读状态机数据返回、无需通过 Raft 协议与集群进行交互的模式，在 etcd 里叫做串行 (Serializable) 读，它具有低延时、高吞吐量的特点，适合对数据一致性要求不高的场景。

我们再看一个对数据敏感性高的场景。

当你发布服务，更新服务的镜像的时候，提交的时候显示更新成功，结果你一刷新页面，发现显示的镜像的还是旧的，再刷新又是新的，这就会导致混乱。再比如说一个转账场景，Alice 给 Bob 转账成功，钱被正常扣出，一刷新页面发现钱又回来了，这也是令人不可接受的。以上的业务场景就对数据准确性要求极高了，在 etcd 里面，提供了一种线性读模式来解决对数据一致性要求高的场景。

什么是线性读呢?

你可以理解一旦一个值更新成功，随后任何通过线性读的 client 都能及时访问到。虽然集群中有多个节点，但 client 通过线性读就如访问一个节点一样。etcd 默认读模式是线性读，因为它需要经过 Raft 协议模块，反应的是集群共识，因此在延时和吞吐量上相比串行读略差一点，适用于对数据一致性要求高的场景。如果你的 etcd 读请求显示指定了是串行读，就不会经过架构图流程中的流程三、四。默认是线性读，因此接下来我们看看读请求进入线性读模块，它是如何工作的。


### 线性读之 ReadIndex
前面我们聊到串行读时提到，它之所以能读到旧数据，主要原因是 Follower 节点收到 Leader 节点同步的写请求后，应用日志条目到状态机是个异步过程，那么我们能否有一种机制在读取的时候，确保最新的数据已经应用到状态机中？

![img](https://static001.geekbang.org/resource/image/1c/cc/1c065788051c6eaaee965575a04109cc.png?wh=1920*1095)

其实这个机制就是叫 ReadIndex，它是在 etcd 3.1 中引入的，我把简化后的原理图放在了上面。当收到一个线性读请求时，它首先会从 Leader 获取集群最新的已提交的日志索引 (committed index)，如上图中的流程二所示。Leader 收到 ReadIndex 请求时，为防止脑裂等异常场景，会向 Follower 节点发送心跳确认，一半以上节点确认 Leader 身份后才能将已提交的索引 (committed index) 返回给节点 C(上图中的流程三)。C 节点则会等待，直到状态机已应用索引 (applied index) 大于等于 Leader 的已提交索引时 (committed Index)(上图中的流程四)，然后去通知读请求，数据已赶上 Leader，你可以去状态机中访问数据了 (上图中的流程五)。以上就是线性读通过 ReadIndex 机制保证数据一致性原理， 当然还有其它机制也能实现线性读，如在早期 etcd 3.0 中读请求通过走一遍 Raft 协议保证一致性， 这种 Raft log read 机制依赖磁盘 IO， 性能相比 ReadIndex 较差。总体而言，KVServer 模块收到线性读请求后，通过架构图中流程三向 Raft 模块发起 ReadIndex 请求，Raft 模块将 Leader 最新的已提交日志索引封装在流程四的 ReadState 结构体，通过 channel 层层返回给线性读模块，线性读模块等待本节点状态机追赶上 Leader 进度，追赶完成后，就通知 KVServer 模块，进行架构图中流程五，与状态机中的 MVCC 模块进行进行交互了。


### MVCC
流程五中的多版本并发控制 (Multiversion concurrency control) 模块是为了解决上一讲我们提到 etcd v2 不支持保存 key 的历史版本、不支持多 key 事务等问题而产生的。它核心由内存树形索引模块 (treeIndex) 和嵌入式的 KV 持久化存储库 boltdb 组成。首先我们需要简单了解下 boltdb，它是个基于 B+ tree 实现的 key-value 键值库，支持事务，提供 Get/Put 等简易 API 给 etcd 操作。那么 etcd 如何基于 boltdb 保存一个 key 的多个历史版本呢?

比如我们现在有以下方案：方案 1 是一个 key 保存多个历史版本的值；方案 2 每次修改操作，生成一个新的版本号 (revision)，以版本号为 key， value 为用户 key-value 等信息组成的结构体。很显然方案 1 会导致 value 较大，存在明显读写放大、并发冲突等问题，而方案 2 正是 etcd 所采用的。boltdb 的 key 是全局递增的版本号 (revision)，value 是用户 key、value 等字段组合成的结构体，然后通过 treeIndex 模块来保存用户 key 和版本号的映射关系。treeIndex 与 boltdb 关系如下面的读事务流程图所示，从 treeIndex 中获取 key hello 的版本号，再以版本号作为 boltdb 的 key，从 boltdb 中获取其 value 信息。

![img](https://static001.geekbang.org/resource/image/4e/a3/4e2779c265c1da1f7209b5293e3789a3.png?wh=1920*1124)



### treeIndex
treeIndex 模块是基于 Google 开源的内存版 btree 库实现的，为什么 etcd 选择上图中的 B-tree 数据结构保存用户 key 与版本号之间的映射关系，而不是哈希表、二叉树呢？在后面的课程中我会再和你介绍。treeIndex 模块只会保存用户的 key 和相关版本号信息，用户 key 的 value 数据存储在 boltdb 里面，相比 ZooKeeper 和 etcd v2 全内存存储，etcd v3 对内存要求更低。

简单介绍了 etcd 如何保存 key 的历史版本后，架构图中流程六也就非常容易理解了， 它需要从 treeIndex 模块中获取 hello 这个 key 对应的版本号信息。treeIndex 模块基于 B-tree 快速查找此 key，返回此 key 对应的索引项 keyIndex 即可。索引项中包含版本号等信息。


### buffer
在获取到版本号信息后，就可从 boltdb 模块中获取用户的 key-value 数据了。不过有一点你要注意，并不是所有请求都一定要从 boltdb 获取数据。etcd 出于数据一致性、性能等考虑，在访问 boltdb 前，首先会从一个内存读事务 buffer 中，二分查找你要访问 key 是否在 buffer 里面，若命中则直接返回。

### boltdb
若 buffer 未命中，此时就真正需要向 boltdb 模块查询数据了，进入了流程七。我们知道 MySQL 通过 table 实现不同数据逻辑隔离，那么在 boltdb 是如何隔离集群元数据与用户数据的呢？答案是 bucket。boltdb 里每个 bucket 类似对应 MySQL 一个表，用户的 key 数据存放的 bucket 名字的是 key，etcd MVCC 元数据存放的 bucket 是 meta。因 boltdb 使用 B+ tree 来组织用户的 key-value 数据，获取 bucket key 对象后，通过 boltdb 的游标 Cursor 可快速在 B+ tree 找到 key hello 对应的 value 数据，返回给 client。到这里，一个读请求之路执行完成。


### 小结
最后我们来小结一下，一个读请求从 client 通过 Round-robin 负载均衡算法，选择一个 etcd server 节点，发出 gRPC 请求，经过 etcd server 的 KVServer 模块、线性读模块、MVCC 的 treeIndex 和 boltdb 模块紧密协作，完成了一个读请求。通过一个读请求，我带你初步了解了 etcd 的基础架构以及各个模块之间是如何协作的。在这过程中，我想和你特别总结下 client 的节点故障自动转移和线性读。一方面， client 的通过负载均衡、错误处理等机制实现了 etcd 节点之间的故障的自动转移，它可助你的业务实现服务高可用，建议使用 etcd 3.4 分支的 client 版本。另一方面，我详细解释了 etcd 提供的两种读机制 (串行读和线性读) 原理和应用场景。通过线性读，对业务而言，访问多个节点的 etcd 集群就如访问一个节点一样简单，能简洁、快速的获取到集群最新共识数据。早期 etcd 线性读使用的 Raft log read，也就是说把读请求像写请求一样走一遍 Raft 的协议，基于 Raft 的日志的有序性，实现线性读。但此方案读涉及磁盘 IO 开销，性能较差，后来实现了 ReadIndex 读机制来提升读性能，满足了 Kubernetes 等业务的诉求。


## 03 | 基础架构：etcd一个写请求是如何执行的？

在上一节课里，我通过分析 etcd 的一个读请求执行流程，给你介绍了 etcd 的基础架构，让你初步了解了在 etcd 的读请求流程中，各个模块是如何紧密协作，执行查询语句，返回数据给 client。那么 etcd 一个写请求执行流程又是怎样的呢？在执行写请求过程中，如果进程 crash 了，如何保证数据不丢、命令不重复执行呢？今天我就和你聊聊 etcd 写过程中是如何解决这些问题的。希望通过这节课，让你了解一个 key-value 写入的原理，对 etcd 的基础架构中涉及写请求相关的模块有一定的理解，同时能触类旁通，当你在软件项目开发过程中遇到类似数据安全、幂等性等问题时，能设计出良好的方案解决它。


### 整体架构

![img](https://static001.geekbang.org/resource/image/8b/72/8b6dfa84bf8291369ea1803387906c72.png?wh=1920*1265)

为了让你能够更直观地理解 etcd 的写请求流程，我在如上的架构图中，用序号标识了下面的一个 put hello 为 world 的写请求的简要执行流程，帮助你从整体上快速了解一个写请求的全貌。

```
etcdctl put hello world --endpoints http://127.0.0.1:2379
OK
```

首先 client 端通过负载均衡算法选择一个 etcd 节点，发起 gRPC 调用。然后 etcd 节点收到请求后经过 gRPC 拦截器、Quota 模块后，进入 KVServer 模块，KVServer 模块向 Raft 模块提交一个提案，提案内容为“大家好，请使用 put 方法执行一个 key 为 hello，value 为 world 的命令”。随后此提案通过 RaftHTTP 网络模块转发、经过集群多数节点持久化后，状态会变成已提交，etcdserver 从 Raft 模块获取已提交的日志条目，传递给 Apply 模块，Apply 模块通过 MVCC 模块执行提案内容，更新状态机。与读流程不一样的是写流程还涉及 Quota、WAL、Apply 三个模块。crash-safe 及幂等性也正是基于 WAL 和 Apply 流程的 consistent index 等实现的，因此今天我会重点和你介绍这三个模块。下面就让我们沿着写请求执行流程图，从 0 到 1 分析一个 key-value 是如何安全、幂等地持久化到磁盘的。


### Quota 模块
首先是流程一 client 端发起 gRPC 调用到 etcd 节点，和读请求不一样的是，写请求需要经过流程二 db 配额（Quota）模块，它有什么功能呢？我们先从此模块的一个常见错误说起，你在使用 etcd 过程中是否遇到过"etcdserver: mvcc: database space exceeded"错误呢？我相信只要你使用过 etcd 或者 Kubernetes，大概率见过这个错误。它是指当前 etcd db 文件大小超过了配额，当出现此错误后，你的整个集群将不可写入，只读，对业务的影响非常大。哪些情况会触发这个错误呢？

一方面默认 db 配额仅为 2G，当你的业务数据、写入 QPS、Kubernetes 集群规模增大后，你的 etcd db 大小就可能会超过 2G。另一方面我们知道 etcd v3 是个 MVCC 数据库，保存了 key 的历史版本，当你未配置压缩策略的时候，随着数据不断写入，db 大小会不断增大，导致超限。

最后你要特别注意的是，如果你使用的是 etcd 3.2.10 之前的旧版本，请注意备份可能会触发 boltdb 的一个 Bug，它会导致 db 大小不断上涨，最终达到配额限制。了解完触发 Quota 限制的原因后，我们再详细了解下 Quota 模块它是如何工作的。

当 etcd server 收到 put/txn 等写请求的时候，会首先检查下当前 etcd db 大小加上你请求的 key-value 大小之和是否超过了配额（quota-backend-bytes）。如果超过了配额，它会产生一个告警（Alarm）请求，告警类型是 NO SPACE，并通过 Raft 日志同步给其它节点，告知 db 无空间了，并将告警持久化存储到 db 中。最终，无论是 API 层 gRPC 模块还是负责将 Raft 侧已提交的日志条目应用到状态机的 Apply 模块，都拒绝写入，集群只读。那遇到这个错误时应该如何解决呢？


首先当然是调大配额。具体多大合适呢？etcd 社区建议不超过 8G。遇到过这个错误的你是否还记得，为什么当你把配额（quota-backend-bytes）调大后，集群依然拒绝写入呢?原因就是我们前面提到的 NO SPACE 告警。Apply 模块在执行每个命令的时候，都会去检查当前是否存在 NO SPACE 告警，如果有则拒绝写入。所以还需要你额外发送一个取消告警（etcdctl alarm disarm）的命令，以消除所有告警。其次你需要检查 etcd 的压缩（compact）配置是否开启、配置是否合理。etcd 保存了一个 key 所有变更历史版本，如果没有一个机制去回收旧的版本，那么内存和 db 大小就会一直膨胀，在 etcd 里面，压缩模块负责回收旧版本的工作。

压缩模块支持按多种方式回收旧版本，比如保留最近一段时间内的历史版本。不过你要注意，它仅仅是将旧版本占用的空间打个空闲（Free）标记，后续新的数据写入的时候可复用这块空间，而无需申请新的空间。如果你需要回收空间，减少 db 大小，得使用碎片整理（defrag）， 它会遍历旧的 db 文件数据，写入到一个新的 db 文件。但是它对服务性能有较大影响，不建议你在生产集群频繁使用。最后你需要注意配额（quota-backend-bytes）的行为，默认'0'就是使用 etcd 默认的 2GB 大小，你需要根据你的业务场景适当调优。如果你填的是个小于 0 的数，就会禁用配额功能，这可能会让你的 db 大小处于失控，导致性能下降，不建议你禁用配额。

### KVServer 模块
通过流程二的配额检查后，请求就从 API 层转发到了流程三的 KVServer 模块的 put 方法，我们知道 etcd 是基于 Raft 算法实现节点间数据复制的，因此它需要将 put 写请求内容打包成一个提案消息，提交给 Raft 模块。不过 KVServer 模块在提交提案前，还有如下的一系列检查和限速。


### Preflight Check
为了保证集群稳定性，避免雪崩，任何提交到 Raft 模块的请求，都会做一些简单的限速判断。如下面的流程图所示，首先，如果 Raft 模块已提交的日志索引（committed index）比已应用到状态机的日志索引（applied index）超过了 5000，那么它就返回一个"etcdserver: too many requests"错误给 client。

![img](https://static001.geekbang.org/resource/image/dc/54/dc8e373e06f2ab5f63a7948c4a6c8554.png?wh=1164*1004)



然后它会尝试去获取请求中的鉴权信息，若使用了密码鉴权、请求中携带了 token，如果 token 无效，则返回"auth: invalid auth token"错误给 client。其次它会检查你写入的包大小是否超过默认的 1.5MB， 如果超过了会返回"etcdserver: request is too large"错误给给 client。


### Propose
最后通过一系列检查之后，会生成一个唯一的 ID，将此请求关联到一个对应的消息通知 channel，然后向 Raft 模块发起（Propose）一个提案（Proposal），提案内容为“大家好，请使用 put 方法执行一个 key 为 hello，value 为 world 的命令”，也就是整体架构图里的流程四。向 Raft 模块发起提案后，KVServer 模块会等待此 put 请求，等待写入结果通过消息通知 channel 返回或者超时。etcd 默认超时时间是 7 秒（5 秒磁盘 IO 延时 +2*1 秒竞选超时时间），如果一个请求超时未返回结果，则可能会出现你熟悉的 etcdserver: request timed out 错误。

### WAL 模块
Raft 模块收到提案后，如果当前节点是 Follower，它会转发给 Leader，只有 Leader 才能处理写请求。Leader 收到提案后，通过 Raft 模块输出待转发给 Follower 节点的消息和待持久化的日志条目，日志条目则封装了我们上面所说的 put hello 提案内容。etcdserver 从 Raft 模块获取到以上消息和日志条目后，作为 Leader，它会将 put 提案消息广播给集群各个节点，同时需要把集群 Leader 任期号、投票信息、已提交索引、提案内容持久化到一个 WAL（Write Ahead Log）日志文件中，用于保证集群的一致性、可恢复性，也就是我们图中的流程五模块。WAL 日志结构是怎样的呢？

![img](https://static001.geekbang.org/resource/image/47/8d/479dec62ed1c31918a7c6cab8e6aa18d.png?wh=1920*1335)

上图是 WAL 结构，它由多种类型的 WAL 记录顺序追加写入组成，每个记录由类型、数据、循环冗余校验码组成。不同类型的记录通过 Type 字段区分，Data 为对应记录内容，CRC 为循环校验码信息。WAL 记录类型目前支持 5 种，分别是文件元数据记录、日志条目记录、状态信息记录、CRC 记录、快照记录：

文件元数据记录包含节点 ID、集群 ID 信息，它在 WAL 文件创建的时候写入；日志条目记录包含 Raft 日志信息，如 put 提案内容；状态信息记录，包含集群的任期号、节点投票信息等，一个日志文件中会有多条，以最后的记录为准；CRC 记录包含上一个 WAL 文件的最后的 CRC（循环冗余校验码）信息， 在创建、切割 WAL 文件时，作为第一条记录写入到新的 WAL 文件， 用于校验数据文件的完整性、准确性等；快照记录包含快照的任期号、日志索引信息，用于检查快照文件的准确性。

WAL 模块又是如何持久化一个 put 提案的日志条目类型记录呢?首先我们来看看 put 写请求如何封装在 Raft 日志条目里面。下面是 Raft 日志条目的数据结构信息，它由以下字段组成：

Term 是 Leader 任期号，随着 Leader 选举增加；Index 是日志条目的索引，单调递增增加；Type 是日志类型，比如是普通的命令日志（EntryNormal）还是集群配置变更日志（EntryConfChange）；Data 保存我们上面描述的 put 提案内容。



```
type Entry struct {
   Term             uint64    `protobuf:"varint，2，opt，name=Term" json:"Term"`
   Index            uint64    `protobuf:"varint，3，opt，name=Index" json:"Index"`
   Type             EntryType `protobuf:"varint，1，opt，name=Type，enum=Raftpb.EntryType" json:"Type"`
   Data             []byte    `protobuf:"bytes，4，opt，name=Data" json:"Data，omitempty"`
}

```
了解完 Raft 日志条目数据结构后，我们再看 WAL 模块如何持久化 Raft 日志条目。它首先先将 Raft 日志条目内容（含任期号、索引、提案内容）序列化后保存到 WAL 记录的 Data 字段， 然后计算 Data 的 CRC 值，设置 Type 为 Entry Type， 以上信息就组成了一个完整的 WAL 记录。最后计算 WAL 记录的长度，顺序先写入 WAL 长度（Len Field），然后写入记录内容，调用 fsync 持久化到磁盘，完成将日志条目保存到持久化存储中。当一半以上节点持久化此日志条目后， Raft 模块就会通过 channel 告知 etcdserver 模块，put 提案已经被集群多数节点确认，提案状态为已提交，你可以执行此提案内容了。于是进入流程六，etcdserver 模块从 channel 取出提案内容，添加到先进先出（FIFO）调度队列，随后通过 Apply 模块按入队顺序，异步、依次执行提案内容。

### Apply 模块
执行 put 提案内容对应我们架构图中的流程七，其细节图如下。那么 Apply 模块是如何执行 put 请求的呢？若 put 请求提案在执行流程七的时候 etcd 突然 crash 了， 重启恢复的时候，etcd 是如何找回异常提案，再次执行的呢？

![img](https://static001.geekbang.org/resource/image/7f/5b/7f13edaf28yy7a6698e647104771235b.png?wh=1920*641)

核心就是我们上面介绍的 WAL 日志，因为提交给 Apply 模块执行的提案已获得多数节点确认、持久化，etcd 重启时，会从 WAL 中解析出 Raft 日志条目内容，追加到 Raft 日志的存储中，并重放已提交的日志提案给 Apply 模块执行。然而这又引发了另外一个问题，如何确保幂等性，防止提案重复执行导致数据混乱呢?

我们在上一节课里讲到，etcd 是个 MVCC 数据库，每次更新都会生成新的版本号。如果没有幂等性保护，同样的命令，一部分节点执行一次，一部分节点遭遇异常故障后执行多次，则系统的各节点一致性状态无法得到保证，导致数据混乱，这是严重故障。因此 etcd 必须要确保幂等性。怎么做呢？Apply 模块从 Raft 模块获得的日志条目信息里，是否有唯一的字段能标识这个提案？答案就是我们上面介绍 Raft 日志条目中的索引（index）字段。日志条目索引是全局单调递增的，每个日志条目索引对应一个提案， 如果一个命令执行后，我们在 db 里面也记录下当前已经执行过的日志条目索引，是不是就可以解决幂等性问题呢？

是的。但是这还不够安全，如果执行命令的请求更新成功了，更新 index 的请求却失败了，是不是一样会导致异常？因此我们在实现上，还需要将两个操作作为原子性事务提交，才能实现幂等。

正如我们上面的讨论的这样，etcd 通过引入一个 consistent index 的字段，来存储系统当前已经执行过的日志条目索引，实现幂等性。Apply 模块在执行提案内容前，首先会判断当前提案是否已经执行过了，如果执行了则直接返回，若未执行同时无 db 配额满告警，则进入到 MVCC 模块，开始与持久化存储模块打交道。

### MVCC
Apply 模块判断此提案未执行后，就会调用 MVCC 模块来执行提案内容。MVCC 主要由两部分组成，一个是内存索引模块 treeIndex，保存 key 的历史版本号信息，另一个是 boltdb 模块，用来持久化存储 key-value 数据。那么 MVCC 模块执行 put hello 为 world 命令时，它是如何构建内存索引和保存哪些数据到 db 呢？

#### treeIndex
首先我们来看 MVCC 的索引模块 treeIndex，当收到更新 key hello 为 world 的时候，此 key 的索引版本号信息是怎么生成的呢？需要维护、持久化存储一个全局版本号吗？版本号（revision）在 etcd 里面发挥着重大作用，它是 etcd 的逻辑时钟。etcd 启动的时候默认版本号是 1，随着你对 key 的增、删、改操作而全局单调递增。因为 boltdb 中的 key 就包含此信息，所以 etcd 并不需要再去持久化一个全局版本号。我们只需要在启动的时候，从最小值 1 开始枚举到最大值，未读到数据的时候则结束，最后读出来的版本号即是当前 etcd 的最大版本号 currentRevision。MVCC 写事务在执行 put hello 为 world 的请求时，会基于 currentRevision 自增生成新的 revision 如{2,0}，然后从 treeIndex 模块中查询 key 的创建版本号、修改次数信息。这些信息将填充到 boltdb 的 value 中，同时将用户的 hello key 和 revision 等信息存储到 B-tree，也就是下面简易写事务图的流程一，整体架构图中的流程八。

![img](https://static001.geekbang.org/resource/image/a1/ff/a19a06d8f4cc5e488a114090d84116ff.png?wh=1920*1035)


### boltdb
MVCC 写事务自增全局版本号后生成的 revision{2,0}，它就是 boltdb 的 key，通过它就可以往 boltdb 写数据了，进入了整体架构图中的流程九。boltdb 上一篇我们提过它是一个基于 B+tree 实现的 key-value 嵌入式 db，它通过提供桶（bucket）机制实现类似 MySQL 表的逻辑隔离。在 etcd 里面你通过 put/txn 等 KV API 操作的数据，全部保存在一个名为 key 的桶里面，这个 key 桶在启动 etcd 的时候会自动创建。除了保存用户 KV 数据的 key 桶，etcd 本身及其它功能需要持久化存储的话，都会创建对应的桶。比如上面我们提到的 etcd 为了保证日志的幂等性，保存了一个名为 consistent index 的变量在 db 里面，它实际上就存储在元数据（meta）桶里面。那么写入 boltdb 的 value 含有哪些信息呢？

写入 boltdb 的 value， 并不是简单的"world"，如果只存一个用户 value，索引又是保存在易失的内存上，那重启 etcd 后，我们就丢失了用户的 key 名，无法构建 treeIndex 模块了。因此为了构建索引和支持 Lease 等特性，etcd 会持久化以下信息:

key 名称；key 创建时的版本号（create_revision）、最后一次修改时的版本号（mod_revision）、key 自身修改的次数（version）；value 值；租约信息（后面介绍）。

boltdb value 的值就是将含以上信息的结构体序列化成的二进制数据，然后通过 boltdb 提供的 put 接口，etcd 就快速完成了将你的数据写入 boltdb，对应上面简易写事务图的流程二。但是 put 调用成功，就能够代表数据已经持久化到 db 文件了吗？

这里需要注意的是，在以上流程中，etcd 并未提交事务（commit），因此数据只更新在 boltdb 所管理的内存数据结构中。事务提交的过程，包含 B+tree 的平衡、分裂，将 boltdb 的脏数据（dirty page）、元数据信息刷新到磁盘，因此事务提交的开销是昂贵的。如果我们每次更新都提交事务，etcd 写性能就会较差。那么解决的办法是什么呢？etcd 的解决方案是合并再合并。

首先 boltdb key 是版本号，put/delete 操作时，都会基于当前版本号递增生成新的版本号，因此属于顺序写入，可以调整 boltdb 的 bucket.FillPercent 参数，使每个 page 填充更多数据，减少 page 的分裂次数并降低 db 空间。其次 etcd 通过合并多个写事务请求，通常情况下，是异步机制定时（默认每隔 100ms）将批量事务一次性提交（pending 事务过多才会触发同步提交）， 从而大大提高吞吐量，对应上面简易写事务图的流程三。

但是这优化又引发了另外的一个问题， 因为事务未提交，读请求可能无法从 boltdb 获取到最新数据。为了解决这个问题，etcd 引入了一个 bucket buffer 来保存暂未提交的事务数据。在更新 boltdb 的时候，etcd 也会同步数据到 bucket buffer。因此 etcd 处理读请求的时候会优先从 bucket buffer 里面读取，其次再从 boltdb 读，通过 bucket buffer 实现读写性能提升，同时保证数据一致性。

### 小结
最后我们来小结一下，今天我给你介绍了 etcd 的写请求流程，重点介绍了 Quota、WAL、Apply 模块。首先我们介绍了 Quota 模块工作原理和我们熟悉的 database space exceeded 错误触发原因，写请求导致 db 大小增加、compact 策略不合理、boltdb Bug 等都会导致 db 大小超限。其次介绍了 WAL 模块的存储结构，它由一条条记录顺序写入组成，每个记录含有 Type、CRC、Data，每个提案被提交前都会被持久化到 WAL 文件中，以保证集群的一致性和可恢复性。

随后我们介绍了 Apply 模块基于 consistent index 和事务实现了幂等性，保证了节点在异常情况下不会重复执行重放的提案。最后我们介绍了 MVCC 模块是如何维护索引版本号、重启后如何从 boltdb 模块中获取内存索引结构的。以及 etcd 通过异步、批量提交事务机制，以提升写 QPS 和吞吐量。通过以上介绍，希望你对 etcd 的一个写语句执行流程有个初步的理解，明白 WAL 模块、Apply 模块、MVCC 模块三者是如何相互协作的，从而实现在节点遭遇 crash 等异常情况下，不丢任何已提交的数据、不重复执行任何提案。

## 04 | Raft协议：etcd如何实现高可用、数据强一致的？
在前面的 etcd 读写流程学习中，我和你多次提到了 etcd 是基于 Raft 协议实现高可用、数据强一致性的。那么 etcd 是如何基于 Raft 来实现高可用、数据强一致性的呢？这节课我们就以上一节中的 hello 写请求为案例，深入分析 etcd 在遇到 Leader 节点 crash 等异常后，Follower 节点如何快速感知到异常，并高效选举出新的 Leader，对外提供高可用服务的。同时，我将通过一个日志复制整体流程图，为你介绍 etcd 如何保障各节点数据一致性，并介绍 Raft 算法为了确保数据一致性、完整性，对 Leader 选举和日志复制所增加的一系列安全规则。希望通过这节课，让你了解 etcd 在节点故障、网络分区等异常场景下是如何基于 Raft 算法实现高可用、数据强一致的。

### 如何避免单点故障
在介绍 Raft 算法之前，我们首先了解下它的诞生背景，Raft 解决了分布式系统什么痛点呢？首先我们回想下，早期我们使用的数据存储服务，它们往往是部署在单节点上的。但是单节点存在单点故障，一宕机就整个服务不可用，对业务影响非常大。随后，为了解决单点问题，软件系统工程师引入了数据复制技术，实现多副本。通过数据复制方案，一方面我们可以提高服务可用性，避免单点故障。另一方面，多副本可以提升读吞吐量、甚至就近部署在业务所在的地理位置，降低访问延迟。

**多副本复制是如何实现的呢？**

多副本常用的技术方案主要有主从复制和去中心化复制。主从复制，又分为全同步复制、异步复制、半同步复制，比如 MySQL/Redis 单机主备版就基于主从复制实现的。


全同步复制是指主收到一个写请求后，必须等待全部从节点确认返回后，才能返回给客户端成功。因此如果一个从节点故障，整个系统就会不可用。这种方案为了保证多副本的一致性，而牺牲了可用性，一般使用不多。

异步复制是指主收到一个写请求后，可及时返回给 client，异步将请求转发给各个副本，若还未将请求转发到副本前就故障了，则可能导致数据丢失，但是可用性是最高的。

半同步复制介于全同步复制、异步复制之间，它是指主收到一个写请求后，至少有一个副本接收数据后，就可以返回给客户端成功，在数据一致性、可用性上实现了平衡和取舍。

跟主从复制相反的就是去中心化复制，它是指在一个 n 副本节点集群中，任意节点都可接受写请求，但一个成功的写入需要 w 个节点确认，读取也必须查询至少 r 个节点。

你可以根据实际业务场景对数据一致性的敏感度，设置合适 w/r 参数。比如你希望每次写入后，任意 client 都能读取到新值，如果 n 是 3 个副本，你可以将 w 和 r 设置为 2，这样当你读两个节点时候，必有一个节点含有最近写入的新值，这种读我们称之为法定票数读（quorum read）。AWS 的 Dynamo 系统就是基于去中心化的复制算法实现的。它的优点是节点角色都是平等的，降低运维复杂度，可用性更高。但是缺陷是去中心化复制，势必会导致各种写入冲突，业务需要关注冲突处理。从以上分析中，为了解决单点故障，从而引入了多副本。但基于复制算法实现的数据库，为了保证服务可用性，大多数提供的是最终一致性，总而言之，不管是主从复制还是异步复制，都存在一定的缺陷。


如何解决以上复制算法的困境呢？

答案就是共识算法，它最早是基于复制状态机背景下提出来的。 下图是复制状态机的结构（引用自 Raft paper）， 它由共识模块、日志模块、状态机组成。通过共识模块保证各个节点日志的一致性，然后各个节点基于同样的日志、顺序执行指令，最终各个复制状态机的结果实现一致。

![img](https://static001.geekbang.org/resource/image/3y/eb/3yy3fbc1ab564e3af9ac9223db1435eb.png?wh=605*319)



共识算法的祖师爷是 Paxos， 但是由于它过于复杂，难于理解，工程实践上也较难落地，导致在工程界落地较慢。standford 大学的 Diego 提出的 Raft 算法正是为了可理解性、易实现而诞生的，它通过问题分解，将复杂的共识问题拆分成三个子问题，分别是：Leader 选举，Leader 故障后集群能快速选出新 Leader；日志复制， 集群只有 Leader 能写入日志， Leader 负责复制日志到 Follower 节点，并强制 Follower 节点与自己保持相同；安全性，一个任期内集群只能产生一个 Leader、已提交的日志条目在发生 Leader 选举时，一定会存在更高任期的新 Leader 日志中、各个节点的状态机应用的任意位置的日志条目内容应一样等。

下面我以实际场景为案例，分别和你深入讨论这三个子问题，看看 Raft 是如何解决这三个问题，以及在 etcd 中的应用实现。


### Leader 选举
当 etcd server 收到 client 发起的 put hello 写请求后，KV 模块会向 Raft 模块提交一个 put 提案，我们知道只有集群 Leader 才能处理写提案，如果此时集群中无 Leader， 整个请求就会超时。那么 Leader 是怎么诞生的呢？Leader crash 之后其他节点如何竞选呢？首先在 Raft 协议中它定义了集群中的如下节点状态，任何时刻，每个节点肯定处于其中一个状态：

Follower，跟随者， 同步从 Leader 收到的日志，etcd 启动的时候默认为此状态；Candidate，竞选者，可以发起 Leader 选举；Leader，集群领导者， 唯一性，拥有同步日志的特权，需定时广播心跳给 Follower 节点，以维持领导者身份。

![img](https://static001.geekbang.org/resource/image/a5/09/a5a210eec289d8e4e363255906391009.png?wh=1808*978)

上图是节点状态变化关系图，当 Follower 节点接收 Leader 节点心跳消息超时后，它会转变成 Candidate 节点，并可发起竞选 Leader 投票，若获得集群多数节点的支持后，它就可转变成 Leader 节点。下面我以 Leader crash 场景为案例，给你详细介绍一下 etcd Leader 选举原理。假设集群总共 3 个节点，A 节点为 Leader，B、C 节点为 Follower。

![img](https://static001.geekbang.org/resource/image/a2/59/a20ba5b17de79d6ce8c78a712a364359.png?wh=1920*942)

如上 Leader 选举图左边部分所示， 正常情况下，Leader 节点会按照心跳间隔时间，定时广播心跳消息（MsgHeartbeat 消息）给 Follower 节点，以维持 Leader 身份。 Follower 收到后回复心跳应答包消息（MsgHeartbeatResp 消息）给 Leader。细心的你可能注意到上图中的 Leader 节点下方有一个任期号（term）， 它具有什么样的作用呢？这是因为 Raft 将时间划分成一个个任期，任期用连续的整数表示，每个任期从一次选举开始，赢得选举的节点在该任期内充当 Leader 的职责，随着时间的消逝，集群可能会发生新的选举，任期号也会单调递增。通过任期号，可以比较各个节点的数据新旧、识别过期的 Leader 等，它在 Raft 算法中充当逻辑时钟，发挥着重要作用。了解完正常情况下 Leader 维持身份的原理后，我们再看异常情况下，也就 Leader crash 后，etcd 是如何自愈的呢？如上 Leader 选举图右边部分所示，当 Leader 节点异常后，Follower 节点会接收 Leader 的心跳消息超时，当超时时间大于竞选超时时间后，它们会进入 Candidate 状态。这里要提醒下你，etcd 默认心跳间隔时间（heartbeat-interval）是 100ms， 默认竞选超时时间（election timeout）是 1000ms， 你需要根据实际部署环境、业务场景适当调优，否则就很可能会频繁发生 Leader 选举切换，导致服务稳定性下降，后面我们实践篇会再详细介绍。进入 Candidate 状态的节点，会立即发起选举流程，自增任期号，投票给自己，并向其他节点发送竞选 Leader 投票消息（MsgVote）。C 节点收到 Follower B 节点竞选 Leader 消息后，这时候可能会出现如下两种情况：

第一种情况是 C 节点判断 B 节点的数据至少和自己一样新、B 节点任期号大于 C 当前任期号、并且 C 未投票给其他候选者，就可投票给 B。这时 B 节点获得了集群多数节点支持，于是成为了新的 Leader。第二种情况是，恰好 C 也心跳超时超过竞选时间了，它也发起了选举，并投票给了自己，那么它将拒绝投票给 B，这时谁也无法获取集群多数派支持，只能等待竞选超时，开启新一轮选举。Raft 为了优化选票被瓜分导致选举失败的问题，引入了随机数，每个节点等待发起选举的时间点不一致，优雅的解决了潜在的竞选活锁，同时易于理解。



Leader 选出来后，它什么时候又会变成 Follower 状态呢？ 从上面的状态转换关系图中你可以看到，如果现有 Leader 发现了新的 Leader 任期号，那么它就需要转换到 Follower 节点。A 节点 crash 后，再次启动成为 Follower，假设因为网络问题无法连通 B、C 节点，这时候根据状态图，我们知道它将不停自增任期号，发起选举。等 A 节点网络异常恢复后，那么现有 Leader 收到了新的任期号，就会触发新一轮 Leader 选举，影响服务的可用性。然而 A 节点的数据是远远落后 B、C 的，是无法获得集群 Leader 地位的，发起的选举无效且对集群稳定性有伤害。那如何避免以上场景中的无效的选举呢？

在 etcd 3.4 中，etcd 引入了一个 PreVote 参数（默认 false），可以用来启用 PreCandidate 状态解决此问题，如下图所示。Follower 在转换成 Candidate 状态前，先进入 PreCandidate 状态，不自增任期号， 发起预投票。若获得集群多数节点认可，确定有概率成为 Leader 才能进入 Candidate 状态，发起选举流程。

![img](https://static001.geekbang.org/resource/image/16/06/169ae84055byya38b616d2e71cfb9706.png?wh=1920*971)

因 A 节点数据落后较多，预投票请求无法获得多数节点认可，因此它就不会进入 Candidate 状态，导致集群重新选举。这就是 Raft Leader 选举核心原理，使用心跳机制维持 Leader 身份、触发 Leader 选举，etcd 基于它实现了高可用，只要集群一半以上节点存活、可相互通信，Leader 宕机后，就能快速选举出新的 Leader，继续对外提供服务。

### 日志复制
假设在上面的 Leader 选举流程中，B 成为了新的 Leader，它收到 put 提案后，它是如何将日志同步给 Follower 节点的呢？ 什么时候它可以确定一个日志条目为已提交，通知 etcdserver 模块应用日志条目指令到状态机呢？这就涉及到 Raft 日志复制原理，为了帮助你理解日志复制的原理，下面我给你画了一幅 Leader 收到 put 请求后，向 Follower 节点复制日志的整体流程图，简称流程图，在图中我用序号给你标识了核心流程。我将结合流程图、后面的 Raft 的日志图和你简要分析 Leader B 收到 put hello 为 world 的请求后，是如何将此请求同步给其他 Follower 节点的。

![img](https://static001.geekbang.org/resource/image/a5/83/a57a990cff7ca0254368d6351ae5b983.png?wh=1920*1327)

首先 Leader 收到 client 的请求后，etcdserver 的 KV 模块会向 Raft 模块提交一个 put hello 为 world 提案消息（流程图中的序号 2 流程），  它的消息类型是 MsgProp。Leader 的 Raft 模块获取到 MsgProp 提案消息后，为此提案生成一个日志条目，追加到未持久化、不稳定的 Raft 日志中，随后会遍历集群 Follower 列表和进度信息，为每个 Follower 生成追加（MsgApp）类型的 RPC 消息，此消息中包含待复制给 Follower 的日志条目。这里就出现两个疑问了。第一，Leader 是如何知道从哪个索引位置发送日志条目给 Follower，以及 Follower 已复制的日志最大索引是多少呢？第二，日志条目什么时候才会追加到稳定的 Raft 日志中呢？Raft 模块负责持久化吗？首先我来给你介绍下什么是 Raft 日志。下图是 Raft 日志复制过程中的日志细节图，简称日志图 1。在日志图中，最上方的是日志条目序号 / 索引，日志由有序号标识的一个个条目组成，每个日志条目内容保存了 Leader 任期号和提案内容。最开始的时候，A 节点是 Leader，任期号为 1，A 节点 crash 后，B 节点通过选举成为新的 Leader， 任期号为 2。日志图 1 描述的是 hello 日志条目未提交前的各节点 Raft 日志状态。

![img](https://static001.geekbang.org/resource/image/3d/87/3dd2b6042e6e0cc86f96f24764b7f587.png?wh=1920*1003)

我们现在就可以来回答第一个疑问了。Leader 会维护两个核心字段来追踪各个 Follower 的进度信息，一个字段是 NextIndex， 它表示 Leader 发送给 Follower 节点的下一个日志条目索引。一个字段是 MatchIndex， 它表示 Follower 节点已复制的最大日志条目的索引，比如上面的日志图 1 中 C 节点的已复制最大日志条目索引为 5，A 节点为 4。我们再看第二个疑问。etcd Raft 模块设计实现上抽象了网络、存储、日志等模块，它本身并不会进行网络、存储相关的操作，上层应用需结合自己业务场景选择内置的模块或自定义实现网络、存储、日志等模块。上层应用通过 Raft 模块的输出接口（如 Ready 结构），获取到待持久化的日志条目和待发送给 Peer 节点的消息后（如上面的 MsgApp 日志消息），需持久化日志条目到自定义的 WAL 模块，通过自定义的网络模块将消息发送给 Peer 节点。日志条目持久化到稳定存储中后，这时候你就可以将日志条目追加到稳定的 Raft 日志中。即便这个日志是内存存储，节点重启时也不会丢失任何日志条目，因为 WAL 模块已持久化此日志条目，可通过它重建 Raft 日志。etcd Raft 模块提供了一个内置的内存存储（MemoryStorage）模块实现，etcd 使用的就是它，Raft 日志条目保存在内存中。网络模块并未提供内置的实现，etcd 基于 HTTP 协议实现了 peer 节点间的网络通信，并根据消息类型，支持选择 pipeline、stream 等模式发送，显著提高了网络吞吐量、降低了延时。解答完以上两个疑问后，我们继续分析 etcd 是如何与 Raft 模块交互，获取待持久化的日志条目和发送给 peer 节点的消息。

正如刚刚讲到的，Raft 模块输入是 Msg 消息，输出是一个 Ready 结构，它包含待持久化的日志条目、发送给 peer 节点的消息、已提交的日志条目内容、线性查询结果等 Raft 输出核心信息。etcdserver 模块通过 channel 从 Raft 模块获取到 Ready 结构后（流程图中的序号 3 流程），因 B 节点是 Leader，它首先会通过基于 HTTP 协议的网络模块将追加日志条目消息（MsgApp）广播给 Follower，并同时将待持久化的日志条目持久化到 WAL 文件中（流程图中的序号 4 流程），最后将日志条目追加到稳定的 Raft 日志存储中（流程图中的序号 5 流程）。各个 Follower 收到追加日志条目（MsgApp）消息，并通过安全检查后，它会持久化消息到 WAL 日志中，并将消息追加到 Raft 日志存储，随后会向 Leader 回复一个应答追加日志条目（MsgAppResp）的消息，告知 Leader 当前已复制的日志最大索引（流程图中的序号 6 流程）。Leader 收到应答追加日志条目（MsgAppResp）消息后，会将 Follower 回复的已复制日志最大索引更新到跟踪 Follower 进展的 Match Index 字段，如下面的日志图 2 中的 Follower C MatchIndex 为 6，Follower A 为 5，日志图 2 描述的是 hello 日志条目提交后的各节点 Raft 日志状态。

![img](https://static001.geekbang.org/resource/image/eb/63/ebbf739a94f9300a85f21da7e55f1e63.png?wh=1920*994)



最后 Leader 根据 Follower 的 MatchIndex 信息，计算出一个位置，如果这个位置已经被一半以上节点持久化，那么这个位置之前的日志条目都可以被标记为已提交。在我们这个案例中日志图 2 里 6 号索引位置之前的日志条目已被多数节点复制，那么他们状态都可被设置为已提交。Leader 可通过在发送心跳消息（MsgHeartbeat）给 Follower 节点时，告知它已经提交的日志索引位置。最后各个节点的 etcdserver 模块，可通过 channel 从 Raft 模块获取到已提交的日志条目（流程图中的序号 7 流程），应用日志条目内容到存储状态机（流程图中的序号 8 流程），返回结果给 client。通过以上流程，Leader 就完成了同步日志条目给 Follower 的任务，一个日志条目被确定为已提交的前提是，它需要被 Leader 同步到一半以上节点上。以上就是 etcd Raft 日志复制的核心原理。



### 安全性
介绍完 Leader 选举和日志复制后，最后我们再来看看 Raft 是如何保证安全性的。如果在上面的日志图 2 中，Leader B 在应用日志指令 put hello 为 world 到状态机，并返回给 client 成功后，突然 crash 了，那么 Follower A 和 C 是否都有资格选举成为 Leader 呢？从日志图 2 中我们可以看到，如果 A 成为了 Leader 那么就会导致数据丢失，因为它并未含有刚刚 client 已经写入成功的 put hello 为 world 指令。Raft 算法如何确保面对这类问题时不丢数据和各节点数据一致性呢？这就是 Raft 的第三个子问题需要解决的。Raft 通过给选举和日志复制增加一系列规则，来实现 Raft 算法的安全性。


### 选举规则
当节点收到选举投票的时候，需检查候选者的最后一条日志中的任期号，若小于自己则拒绝投票。如果任期号相同，日志却比自己短，也拒绝为其投票。比如在日志图 2 中，Folllower A 和 C 任期号相同，但是 Follower C 的数据比 Follower A 要长，那么在选举的时候，Follower C 将拒绝投票给 A， 因为它的数据不是最新的。同时，对于一个给定的任期号，最多只会有一个 leader 被选举出来，leader 的诞生需获得集群一半以上的节点支持。每个节点在同一个任期内只能为一个节点投票，节点需要将投票信息持久化，防止异常重启后再投票给其他节点。通过以上规则就可防止日志图 2 中的 Follower A 节点成为 Leader。

### 日志复制规则
在日志图 2 中，Leader B 返回给 client 成功后若突然 crash 了，此时可能还并未将 6 号日志条目已提交的消息通知到 Follower A 和 C，那么如何确保 6 号日志条目不被新 Leader 删除呢？ 同时在 etcd 集群运行过程中，Leader 节点若频繁发生 crash 后，可能会导致 Follower 节点与 Leader 节点日志条目冲突，如何保证各个节点的同 Raft 日志位置含有同样的日志条目？以上各类异常场景的安全性是通过 Raft 算法中的 Leader 完全特性和只附加原则、日志匹配等安全机制来保证的。


Leader 完全特性是指如果某个日志条目在某个任期号中已经被提交，那么这个条目必然出现在更大任期号的所有 Leader 中。Leader 只能追加日志条目，不能删除已持久化的日志条目（只附加原则），因此 Follower C 成为新 Leader 后，会将前任的 6 号日志条目复制到 A 节点。

为了保证各个节点日志一致性，Raft 算法在追加日志的时候，引入了一致性检查。Leader 在发送追加日志 RPC 消息时，会把新的日志条目紧接着之前的条目的索引位置和任期号包含在里面。Follower 节点会检查相同索引位置的任期号是否与 Leader 一致，一致才能追加，这就是日志匹配特性。它本质上是一种归纳法，一开始日志空满足匹配特性，随后每增加一个日志条目时，都要求上一个日志条目信息与 Leader 一致，那么最终整个日志集肯定是一致的。通过以上的 Leader 选举限制、Leader 完全特性、只附加原则、日志匹配等安全特性，Raft 就实现了一个可严格通过数学反证法、归纳法证明的高可用、一致性算法，为 etcd 的安全性保驾护航。


### 小结
最后我们来小结下今天的内容。我从如何避免单点故障说起，给你介绍了分布式系统中实现多副本技术的一系列方案，从主从复制到去中心化复制、再到状态机、共识算法，让你了解了各个方案的优缺点，以及主流存储产品的选择。Raft 虽然诞生晚，但它却是共识算法里面在工程界应用最广泛的。它将一个复杂问题拆分成三个子问题，分别是 Leader 选举、日志复制和安全性。Raft 通过心跳机制、随机化等实现了 Leader 选举，只要集群半数以上节点存活可相互通信，etcd 就可对外提供高可用服务。Raft 日志复制确保了 etcd 多节点间的数据一致性，我通过一个 etcd 日志复制整体流程图为你详细介绍了 etcd 写请求从提交到 Raft 模块，到被应用到状态机执行的各个流程，剖析了日志复制的核心原理，即一个日志条目只有被 Leader 同步到一半以上节点上，此日志条目才能称之为成功复制、已提交。Raft 的安全性，通过对 Leader 选举和日志复制增加一系列规则，保证了整个集群的一致性、完整性。



## 05 | 鉴权：如何保护你的数据安全？

不知道你有没有过这样的困惑，当你使用 etcd 存储业务敏感数据、多租户共享使用同 etcd 集群的时候，应该如何防止匿名用户访问你的 etcd 数据呢？多租户场景又如何最小化用户权限分配，防止越权访问的？etcd 鉴权模块就是为了解决以上痛点而生。那么 etcd 是如何实现多种鉴权机制和细粒度的权限控制的？在实现鉴权模块的过程中最核心的挑战是什么？又该如何确保鉴权的安全性以及提升鉴权性能呢？今天这节课，我将为你介绍 etcd 的鉴权模块，深入剖析 etcd 如何解决上面的这些痛点和挑战。希望通过这节课，帮助你掌握 etcd 鉴权模块的设计、实现精要，了解各种鉴权方案的优缺点。你能在实际应用中，根据自己的业务场景、安全诉求，选择合适的方案保护你的 etcd 数据安全。同时，你也可以参考其设计、实现思想应用到自己业务的鉴权系统上。

### 整体架构
在详细介绍 etcd 的认证、鉴权实现细节之前，我先给你从整体上介绍下 etcd 鉴权体系。etcd 鉴权体系架构由控制面和数据面组成。

![img](https://static001.geekbang.org/resource/image/30/4e/304257ac790aeda91616bfe42800364e.png?wh=1920*420)

上图是是 etcd 鉴权体系控制面，你可以通过客户端工具 etcdctl 和鉴权 API 动态调整认证、鉴权规则，AuthServer 收到请求后，为了确保各节点间鉴权元数据一致性，会通过 Raft 模块进行数据同步。当对应的 Raft 日志条目被集群半数以上节点确认后，Apply 模块通过鉴权存储 (AuthStore) 模块，执行日志条目的内容，将规则存储到 boltdb 的一系列“鉴权表”里面。下图是数据面鉴权流程，由认证和授权流程组成。认证的目的是检查 client 的身份是否合法、防止匿名用户访问等。目前 etcd 实现了两种认证机制，分别是密码认证和证书认证。

![img](https://static001.geekbang.org/resource/image/2c/55/2c8f90fd1a30fab9b9a88ba18c24c555.png?wh=1920*1136)

认证通过后，为了提高密码认证性能，会分配一个 Token（类似我们生活中的门票、通信证）给 client，client 后续其他请求携带此 Token，server 就可快速完成 client 的身份校验工作。实现分配 Token 的服务也有多种，这是 TokenProvider 所负责的，目前支持 SimpleToken 和 JWT 两种。通过认证后，在访问 MVCC 模块之前，还需要通过授权流程。授权的目的是检查 client 是否有权限操作你请求的数据路径，etcd 实现了 RBAC 机制，支持为每个用户分配一个角色，为每个角色授予最小化的权限。

![img](https://static001.geekbang.org/resource/image/8d/8a/8d18f8877ea7c8fbyybebae236a8688a.png?wh=1920*1125)

好了，etcd 鉴权体系的整个流程讲完了，下面我们就以第三节课中提到的 put hello 命令为例，给你深入分析以上鉴权体系是如何进行身份认证来防止匿名访问的，又是如何实现细粒度的权限控制以防止越权访问的。

### 认证
首先我们来看第一个问题，如何防止匿名用户访问你的 etcd 数据呢？解决方案当然是认证用户身份。那 etcd 提供了哪些机制来验证 client 身份呢?正如我整体架构中给你介绍的，etcd 目前实现了两种机制，分别是用户密码认证和证书认证，下面我分别给你介绍这两种机制在 etcd 中如何实现，以及这两种机制各自的优缺点。


#### 密码认证
首先我们来讲讲用户密码认证。etcd 支持为每个用户分配一个账号名称、密码。密码认证在我们生活中无处不在，从银行卡取款到微信、微博 app 登录，再到核武器发射，密码认证应用及其广泛，是最基础的鉴权的方式。但密码认证存在两大难点，它们分别是如何保障密码安全性和提升密码认证性能。

**如何保障密码安全性**

我们首先来看第一个难点：如何保障密码安全性。也许刚刚毕业的你会说直接明文存储，收到用户鉴权请求的时候，检查用户请求中密码与存储中是否一样，不就可以了吗？ 这种方案的确够简单，但你是否想过，若存储密码的文件万一被黑客脱库了，那么所有用户的密码都将被泄露，进而可能会导致重大数据泄露事故。也许你又会说，自己可以奇思妙想构建一个加密算法，然后将密码翻译下，比如将密码中的每个字符按照字母表序替换成字母后的第 XX 个字母。然而这种加密算法，它是可逆的，一旦被黑客识别到规律，还原出你的密码后，脱库后也将导致全部账号数据泄密。那么是否我们用一种不可逆的加密算法就行了呢？比如常见的 MD5，SHA-1，这方案听起来似乎有点道理，然而还是不严谨，因为它们的计算速度非常快，黑客可以通过暴力枚举、字典、彩虹表等手段，快速将你的密码全部破解。LinkedIn 在 2012 年的时候 650 万用户密码被泄露，黑客 3 天就暴力破解出 90% 用户的密码，原因就是 LinkedIn 仅仅使用了 SHA-1 加密算法。

**那应该如何进一步增强不可逆 hash 算法的破解难度？**

一方面我们可以使用安全性更高的 hash 算法，比如 SHA-256，它输出位数更多、计算更加复杂且耗 CPU。另一方面我们可以在每个用户密码 hash 值的计算过程中，引入一个随机、较长的加盐 (salt) 参数，它可以让相同的密码输出不同的结果，这让彩虹表破解直接失效。彩虹表是黑客破解密码的一种方法之一，它预加载了常用密码使用 MD5/SHA-1 计算的 hash 值，可通过 hash 值匹配快速破解你的密码。最后我们还可以增加密码 hash 值计算过程中的开销，比如循环迭代更多次，增加破解的时间成本。

**etcd 的鉴权模块如何安全存储用户密码？**

etcd 的用户密码存储正是融合了以上讨论的高安全性 hash 函数（Blowfish encryption algorithm）、随机的加盐 salt、可自定义的 hash 值计算迭代次数 cost。下面我将通过几个简单 etcd 鉴权 API，为你介绍密码认证的原理。首先你可以通过如下的 auth enable 命令开启鉴权，注意 etcd 会先要求你创建一个 root 账号，它拥有集群的最高读写权限。
```
$ etcdctl user add root:root
User root created
$ etcdctl auth enable
Authentication Enabled
```
启用鉴权后，这时 client 发起如下 put hello 操作时， etcd server 会返回"user name is empty"错误给 client，就初步达到了防止匿名用户访问你的 etcd 数据目的。 那么 etcd server 是在哪里做的鉴权的呢?


```
$ etcdctl put hello world
Error: etcdserver: user name is empty
```
etcd server 收到 put hello 请求的时候，在提交到 Raft 模块前，它会从你请求的上下文中获取你的用户身份信息。如果你未通过认证，那么在状态机应用 put 命令的时候，检查身份权限的时候发现是空，就会返回此错误给 client。下面我通过鉴权模块的 user 命令，给 etcd 增加一个 alice 账号。我们一起来看看 etcd 鉴权模块是如何基于我上面介绍的技术方案，来安全存储 alice 账号信息。

```
$ etcdctl user add alice:alice --user root:root
User alice created
```

鉴权模块收到此命令后，它会使用 bcrpt 库的 blowfish 算法，基于明文密码、随机分配的 salt、自定义的 cost、迭代多次计算得到一个 hash 值，并将加密算法版本、salt 值、cost、hash 值组成一个字符串，作为加密后的密码。最后，鉴权模块将用户名 alice 作为 key，用户名、加密后的密码作为 value，存储到 boltdb 的 authUsers bucket 里面，完成一个账号创建。当你使用 alice 账号访问 etcd 的时候，你需要先调用鉴权模块的 Authenticate 接口，它会验证你的身份合法性。那么 etcd 如何验证你密码正确性的呢？


鉴权模块首先会根据你请求的用户名 alice，从 boltdb 获取加密后的密码，因此 hash 值包含了算法版本、salt、cost 等信息，因此可以根据你请求中的明文密码，计算出最终的 hash 值，若计算结果与存储一致，那么身份校验通过。


### 如何提升密码认证性能
通过以上的鉴权安全性的深入分析，我们知道身份验证这个过程开销极其昂贵，那么问题来了，如何避免频繁、昂贵的密码计算匹配，提升密码认证的性能呢？这就是密码认证的第二个难点，如何保证性能。想想我们办理港澳通行证的时候，流程特别复杂，需要各种身份证明、照片、指纹信息，办理成功后，下发通信证，每次过关你只需要刷下通信证即可，高效而便捷。那么，在软件系统领域如果身份验证通过了后，我们是否也可以返回一个类似通信证的凭据给 client，后续请求携带通信证，只要通行证合法且在有效期内，就无需再次鉴权了呢？是的，etcd 也有类似这样的凭据。当 etcd server 验证用户密码成功后，它就会返回一个 Token 字符串给 client，用于表示用户的身份。后续请求携带此 Token，就无需再次进行密码校验，实现了通信证的效果。etcd 目前支持两种 Token，分别为 Simple Token 和 JWT Token。


#### Simple Token
Simple Token 实现正如名字所言，简单。

Simple Token 的核心原理是当一个用户身份验证通过后，生成一个随机的字符串值 Token 返回给 client，并在内存中使用 map 存储用户和 Token 映射关系。当收到用户的请求时， etcd 会从请求中获取 Token 值，转换成对应的用户名信息，返回给下层模块使用。Token 是你身份的象征，若此 Token 泄露了，那你的数据就可能存在泄露的风险。etcd 是如何应对这种潜在的安全风险呢？etcd 生成的每个 Token，都有一个过期时间 TTL 属性，Token 过期后 client 需再次验证身份，因此可显著缩小数据泄露的时间窗口，在性能上、安全性上实现平衡。在 etcd v3.4.9 版本中，Token 默认有效期是 5 分钟，etcd server 会定时检查你的 Token 是否过期，若过期则从 map 数据结构中删除此 Token。不过你要注意的是，Simple Token 字符串本身并未含任何有价值信息，因此 client 无法及时、准确获取到 Token 过期时间。所以 client 不容易提前去规避因 Token 失效导致的请求报错。从以上介绍中，你觉得 Simple Token 有哪些不足之处？为什么 etcd 社区仅建议在开发、测试环境中使用 Simple Token 呢？首先它是有状态的，etcd server 需要使用内存存储 Token 和用户名的映射关系。其次，它的可描述性很弱，client 无法通过 Token 获取到过期时间、用户名、签发者等信息。etcd 鉴权模块实现的另外一个 Token Provider 方案 JWT，正是为了解决这些不足之处而生。

#### JWT Token
JWT 是 Json Web Token 缩写， 它是一个基于 JSON 的开放标准（RFC 7519）定义的一种紧凑、独立的格式，可用于在身份提供者和服务提供者间，传递被认证的用户身份信息。它由 Header、Payload、Signature 三个对象组成， 每个对象都是一个 JSON 结构体。第一个对象是 Header，它包含 alg 和 typ 两个字段，alg 表示签名的算法，etcd 支持 RSA、ESA、PS 系列，typ 表示类型就是 JWT。

```
{
"alg": "RS256"，
"typ": "JWT"
}
```
第二对象是 Payload，它表示载荷，包含用户名、过期时间等信息，可以自定义添加字段。

```
{
"username": username，
"revision": revision，
"exp":      time.Now().Add(t.ttl).Unix()，
}
```
第三个对象是签名，首先它将 header、payload 使用 base64 url 编码，然后将编码后的字符串用"."连接在一起，最后用我们选择的签名算法比如 RSA 系列的私钥对其计算签名，输出结果即是 Signature。
```
signature=RSA256(
base64UrlEncode(header) + "." +
base64UrlEncode(payload)，
key)
```
JWT 就是由 base64UrlEncode(header).base64UrlEncode(payload).signature 组成。

为什么说 JWT 是独立、紧凑的格式呢？

从以上原理介绍中我们知道，它是无状态的。JWT Token 自带用户名、版本号、过期时间等描述信息，etcd server 不需要保存它，client 可方便、高效的获取到 Token 的过期时间、用户名等信息。它解决了 Simple Token 的若干不足之处，安全性更高，etcd 社区建议大家在生产环境若使用了密码认证，应使用 JWT  Token( --auth-token 'jwt')，而不是默认的 Simple Token。在给你介绍完密码认证实现过程中的两个核心挑战，密码存储安全和性能的解决方案之后，你是否对密码认证的安全性、性能还有所担忧呢？接下来我给你介绍 etcd 的另外一种高性能、更安全的鉴权方案，x509 证书认证。

### 证书认证
密码认证一般使用在 client 和 server 基于 HTTP 协议通信的内网场景中。当对安全有更高要求的时候，你需要使用 HTTPS 协议加密通信数据，防止中间人攻击和数据被篡改等安全风险。HTTPS 是利用非对称加密实现身份认证和密钥协商，因此使用 HTTPS 协议的时候，你需要使用 CA 证书给 client 生成证书才能访问。

那么一个 client 证书包含哪些信息呢？使用证书认证的时候，etcd server 如何知道你发送的请求对应的用户名称？我们可以使用下面的 openssl 命令查看 client 证书的内容，下图是一个 x509 client 证书的内容，它含有证书版本、序列号、签名算法、签发者、有效期、主体名等信息，我们重点要关注的是主体名中的 CN 字段。在 etcd 中，如果你使用了 HTTPS 协议并启用了 client 证书认证 (--client-cert-auth)，它会取 CN 字段作为用户名，在我们的案例中，alice 就是 client 发送请求的用户名。

```
openssl x509 -noout -text -in client.pem
```

![img](https://static001.geekbang.org/resource/image/55/94/55e03b4353c9a467493a3922cf68b294.png?wh=1144*854)

证书认证在稳定性、性能上都优于密码认证。稳定性上，它不存在 Token 过期、使用更加方便、会让你少踩坑，避免了不少 Token 失效而触发的 Bug。性能上，证书认证无需像密码认证一样调用昂贵的密码认证操作 (Authenticate 请求)，此接口支持的性能极低，后面实践篇会和你深入讨论。

### 授权
当我们使用如上创建的 alice 账号执行 put hello 操作的时候，etcd 却会返回如下的"etcdserver: permission denied"无权限错误，这是为什么呢？

```
$ etcdctl put hello world --user alice:alice
Error: etcdserver: permission denied
```

这是因为开启鉴权后，put 请求命令在应用到状态机前，etcd 还会对发出此请求的用户进行权限检查， 判断其是否有权限操作请求的数据。常用的权限控制方法有 ACL(Access Control List)、ABAC(Attribute-based access control)、RBAC(Role-based access control)，etcd 实现的是 RBAC 机制。


### RBAC
什么是基于角色权限的控制系统 (RBAC) 呢？它由下图中的三部分组成，User、Role、Permission。User 表示用户，如 alice。Role 表示角色，它是权限的赋予对象。Permission 表示具体权限明细，比如赋予 Role 对 key 范围在[ key，KeyEnd]数据拥有什么权限。目前支持三种权限，分别是 READ、WRITE、READWRITE。

![img](https://static001.geekbang.org/resource/image/ee/60/ee6e0a9a63aeaa2d3505ab1a37360760.png?wh=1786*1134)

下面我们通过 etcd 的 RBAC 机制，给 alice 用户赋予一个可读写[hello,helly]数据范围的读写权限， 如何操作呢?按照上面介绍的 RBAC 原理，首先你需要创建一个 role，这里我们命名为 admin，然后新增了一个可读写[hello,helly]数据范围的权限给 admin 角色，并将 admin 的角色的权限授予了用户 alice。详细如下：



```
$ #创建一个admin role 
etcdctl role add admin  --user root:root
Role admin created
# #分配一个可读写[hello，helly]范围数据的权限给admin role
$ etcdctl role grant-permission admin readwrite hello helly --user root:root
Role admin updated
# 将用户alice和admin role关联起来，赋予admin权限给user
$ etcdctl user grant-role alice admin --user root:root
Role admin is granted to user alice
```

然后当你再次使用 etcdctl 执行 put hello 命令时，鉴权模块会从 boltdb 查询 alice 用户对应的权限列表。因为有可能一个用户拥有成百上千个权限列表，etcd 为了提升权限检查的性能，引入了区间树，检查用户操作的 key 是否在已授权的区间，时间复杂度仅为 O(logN)。在我们的这个案例中，很明显 hello 在 admin 角色可读写的[hello，helly) 数据范围内，因此它有权限更新 key hello，执行成功。你也可以尝试更新 key hey，因为此 key 未在鉴权的数据区间内，因此 etcd server 会返回"etcdserver: permission denied"错误给 client，如下所示。

```
$ etcdctl put hello world --user alice:alice
OK
$ etcdctl put hey hey --user alice:alice
Error: etcdserver: permission denied
```

### 小结
最后我和你总结下今天的内容，从 etcd 鉴权模块核心原理分析过程中，你会发现设计实现一个鉴权模块最关键的目标和挑战应该是安全、性能以及一致性。首先鉴权目的是为了保证安全，必须防止恶意用户绕过鉴权系统、伪造、篡改、越权等行为，同时设计上要有前瞻性，做到即使被拖库也影响可控。etcd 的解决方案是通过密码安全加密存储、证书认证、RBAC 等机制保证其安全性。然后，鉴权作为了一个核心的前置模块，性能上不能拖后腿，不能成为影响业务性能的一个核心瓶颈。etcd 的解决方案是通过 Token 降低频繁、昂贵的密码验证开销，可应用在内网、小规模业务场景，同时支持使用证书认证，不存在 Token 过期，巧妙的取 CN 字段作为用户名，可满足较大规模的业务场景鉴权诉求。接着，鉴权系统面临的业务场景是复杂的，因此权限控制系统应当具备良好的扩展性，业务可根据自己实际场景选择合适的鉴权方法。etcd 的 Token Provider 和 RBAC 扩展机制，都具备较好的扩展性、灵活性。尤其是 RBAC 机制，让你可以精细化的控制每个用户权限，实现权限最小化分配。最后鉴权系统元数据的存储应当是可靠的，各个节点鉴权数据应确保一致，确保鉴权行为一致性。早期 etcd v2 版本时，因鉴权命令未经过 Raft 模块，存在数据不一致的问题，在 etcd v3 中通过 Raft 模块同步鉴权指令日志指令，实现鉴权数据一致性。


## 06 | 租约：如何检测你的客户端存活？

今天我要跟你分享的主题是租约（Lease）。etcd 的一个典型的应用场景是 Leader 选举，那么 etcd 为什么可以用来实现 Leader 选举？核心特性实现原理又是怎样的？今天我就和你聊聊 Leader 选举背后技术点之一的 Lease， 解析它的核心原理、性能优化思路，希望通过本节让你对 Lease 如何关联 key、Lease 如何高效续期、淘汰、什么是 checkpoint 机制有深入的理解。同时希望你能基于 Lease 的 TTL 特性，解决实际业务中遇到分布式锁、节点故障自动剔除等各类问题，提高业务服务的可用性。


### 什么是 Lease
在实际业务场景中，我们常常会遇到类似 Kubernetes 的调度器、控制器组件同一时刻只能存在一个副本对外提供服务的情况。然而单副本部署的组件，是无法保证其高可用性的。那为了解决单副本的可用性问题，我们就需要多副本部署。同时，为了保证同一时刻只有一个能对外提供服务，我们需要引入 Leader 选举机制。那么 Leader 选举本质是要解决什么问题呢？首先当然是要保证 Leader 的唯一性，确保集群不出现多个 Leader，才能保证业务逻辑准确性，也就是安全性（Safety）、互斥性。其次是主节点故障后，备节点应可快速感知到其异常，也就是活性（liveness）检测。实现活性检测主要有两种方案。方案一为被动型检测，你可以通过探测节点定时拨测 Leader 节点，看是否健康，比如 Redis Sentinel。方案二为主动型上报，Leader 节点可定期向协调服务发送"特殊心跳"汇报健康状态，若其未正常发送心跳，并超过和协调服务约定的最大存活时间后，就会被协调服务移除 Leader 身份标识。同时其他节点可通过协调服务，快速感知到 Leader 故障了，进而发起新的选举。


我们今天的主题，Lease，正是基于主动型上报模式，**提供的一种活性检测机制**。Lease 顾名思义，client 和 etcd server 之间存在一个约定，内容是 etcd server 保证在约定的有效期内（TTL），不会删除你关联到此 Lease 上的 key-value。

若你未在有效期内续租，那么 etcd server 就会删除 Lease 和其关联的 key-value。

你可以基于 Lease 的 TTL 特性，解决类似 Leader 选举、Kubernetes Event 自动淘汰、服务发现场景中故障节点自动剔除等问题。为了帮助你理解 Lease 的核心特性原理，我以一个实际场景中的经常遇到的异常节点自动剔除为案例，围绕这个问题，给你深入介绍 Lease 特性的实现。在这个案例中，我们期望的效果是，在节点异常时，表示节点健康的 key 能被从 etcd 集群中自动删除。

### Lease 整体架构
在和你详细解读 Lease 特性如何解决上面的问题之前，我们先了解下 Lease 模块的整体架构，下图是我给你画的 Lease 模块简要架构图。

![img](https://static001.geekbang.org/resource/image/ac/7c/ac70641fa3d41c2dac31dbb551394b7c.png?wh=2464*1552)



etcd 在启动的时候，创建 Lessor 模块的时候，它会启动两个常驻 goroutine，如上图所示，一个是 RevokeExpiredLease 任务，定时检查是否有过期 Lease，发起撤销过期的 Lease 操作。一个是 CheckpointScheduledLease，定时触发更新 Lease 的剩余到期时间的操作。Lessor 模块提供了 Grant、Revoke、LeaseTimeToLive、LeaseKeepAlive API 给 client 使用，各接口作用如下:

Grant 表示创建一个 TTL 为你指定秒数的 Lease，Lessor 会将 Lease 信息持久化存储在 boltdb 中；Revoke 表示撤销 Lease 并删除其关联的数据；LeaseTimeToLive 表示获取一个 Lease 的有效期、剩余时间；LeaseKeepAlive 表示为 Lease 续期。

### key 如何关联 Lease
了解完整体架构后，我们再看如何基于 Lease 特性实现检测一个节点存活。首先如何为节点健康指标创建一个租约、并与节点健康指标 key 关联呢?如 KV 模块的一样，client 可通过 clientv3 库的 Lease API 发起 RPC 调用，你可以使用如下的 etcdctl 命令为 node 的健康状态指标，创建一个 Lease，有效期为 600 秒。然后通过 timetolive 命令，查看 Lease 的有效期、剩余时间。

```
# 创建一个TTL为600秒的lease，etcd server返回LeaseID
$ etcdctl lease grant 600
lease 326975935f48f814 granted with TTL(600s)


# 查看lease的TTL、剩余时间
$ etcdctl lease timetolive 326975935f48f814
lease 326975935f48f814 granted with TTL(600s)， remaining(590s)
```

当 Lease server 收到 client 的创建一个有效期 600 秒的 Lease 请求后，会通过 Raft 模块完成日志同步，随后 Apply 模块通过 Lessor 模块的 Grant 接口执行日志条目内容。首先 Lessor 的 Grant 接口会把 Lease 保存到内存的 ItemMap 数据结构中，然后它需要持久化 Lease，将 Lease 数据保存到 boltdb 的 Lease bucket 中，返回一个唯一的 LeaseID 给 client。通过这样一个流程，就基本完成了 Lease 的创建。那么节点的健康指标数据如何关联到此 Lease 上呢？很简单，KV 模块的 API 接口提供了一个"--lease"参数，你可以通过如下命令，将 key node 关联到对应的 LeaseID 上。然后你查询的时候增加 -w 参数输出格式为 json，就可查看到 key 关联的 LeaseID。


```
$ etcdctl put node healthy --lease 326975935f48f818
OK
$ etcdctl get node -w=json | python -m json.tool
{
    "kvs":[
        {
            "create_revision":24，
            "key":"bm9kZQ=="，
            "Lease":3632563850270275608，
            "mod_revision":24，
            "value":"aGVhbHRoeQ=="，
            "version":1
        }
    ]
}
```
以上流程原理如下图所示，它描述了用户的 key 是如何与指定 Lease 关联的。当你通过 put 等命令新增一个指定了"--lease"的 key 时，MVCC 模块它会通过 Lessor 模块的 Attach 方法，将 key 关联到 Lease 的 key 内存集合 ItemSet 中。

![img](https://static001.geekbang.org/resource/image/aa/ee/aaf8bf5c3841a641f8c51fcc34ac67ee.png?wh=2024*1450)

一个 Lease 关联的 key 集合是保存在内存中的，那么 etcd 重启时，是如何知道每个 Lease 上关联了哪些 key 呢?答案是 etcd 的 MVCC 模块在持久化存储 key-value 的时候，保存到 boltdb 的 value 是个结构体（mvccpb.KeyValue）， 它不仅包含你的 key-value 数据，还包含了关联的 LeaseID 等信息。因此当 etcd 重启时，可根据此信息，重建关联各个 Lease 的 key 集合列表。


### 如何优化 Lease 续期性能
通过以上流程，我们完成了 Lease 创建和数据关联操作。在正常情况下，你的节点存活时，需要定期发送 KeepAlive 请求给 etcd 续期健康状态的 Lease，否则你的 Lease 和关联的数据就会被删除。那么 Lease 是如何续期的? 作为一个高频率的请求 API，etcd 如何优化 Lease 续期的性能呢？Lease 续期其实很简单，核心是将 Lease 的过期时间更新为当前系统时间加其 TTL。关键问题在于续期的性能能否满足业务诉求。然而影响续期性能因素又是源自多方面的。首先是 TTL，TTL 过长会导致节点异常后，无法及时从 etcd 中删除，影响服务可用性，而过短，则要求 client 频繁发送续期请求。其次是 Lease 数，如果 Lease 成千上万个，那么 etcd 可能无法支撑如此大规模的 Lease 数，导致高负载。


如何解决呢？首先我们回顾下早期 etcd v2 版本是如何实现 TTL 特性的。在早期 v2 版本中，没有 Lease 概念，TTL 属性是在 key 上面，为了保证 key 不删除，即便你的 TTL 相同，client 也需要为每个 TTL、key 创建一个 HTTP/1.x 连接，定时发送续期请求给 etcd server。很显然，v2 老版本这种设计，因不支持连接多路复用、相同 TTL 无法复用导致性能较差，无法支撑较大规模的 Lease 场景。etcd v3 版本为了解决以上问题，提出了 Lease 特性，TTL 属性转移到了 Lease 上， 同时协议从 HTTP/1.x 优化成 gRPC 协议。一方面不同 key 若 TTL 相同，可复用同一个 Lease， 显著减少了 Lease 数。另一方面，通过 gRPC HTTP/2 实现了多路复用，流式传输，同一连接可支持为多个 Lease 续期，大大减少了连接数。通过以上两个优化，实现 Lease 性能大幅提升，满足了各个业务场景诉求。



### 如何高效淘汰过期 Lease
在了解完节点正常情况下的 Lease 续期特性后，我们再看看节点异常时，未正常续期后，etcd 又是如何淘汰过期 Lease、删除节点健康指标 key 的。淘汰过期 Lease 的工作由 Lessor 模块的一个异步 goroutine 负责。如下面架构图虚线框所示，它会定时从最小堆中取出已过期的 Lease，执行删除 Lease 和其关联的 key 列表数据的 RevokeExpiredLease 任务。

![img](https://static001.geekbang.org/resource/image/b0/6b/b09e9d30157876b031ed206391698c6b.png?wh=2552*1550)

从图中你可以看到，目前 etcd 是基于最小堆来管理 Lease，实现快速淘汰过期的 Lease。etcd 早期的时候，淘汰 Lease 非常暴力。etcd 会直接遍历所有 Lease，逐个检查 Lease 是否过期，过期则从 Lease 关联的 key 集合中，取出 key 列表，删除它们，时间复杂度是 O(N)。然而这种方案随着 Lease 数增大，毫无疑问它的性能会变得越来越差。我们能否按过期时间排序呢？这样每次只需轮询、检查排在前面的 Lease 过期时间，一旦轮询到未过期的 Lease， 则可结束本轮检查。刚刚说的就是 etcd Lease 高效淘汰方案最小堆的实现方法。每次新增 Lease、续期的时候，它会插入、更新一个对象到最小堆中，对象含有 LeaseID 和其到期时间 unixnano，对象之间按到期时间升序排序。etcd Lessor 主循环每隔 500ms 执行一次撤销 Lease 检查（RevokeExpiredLease），每次轮询堆顶的元素，若已过期则加入到待淘汰列表，直到堆顶的 Lease 过期时间大于当前，则结束本轮轮询。相比早期 O(N) 的遍历时间复杂度，使用堆后，插入、更新、删除，它的时间复杂度是 O(Log N)，查询堆顶对象是否过期时间复杂度仅为 O(1)，性能大大提升，可支撑大规模场景下 Lease 的高效淘汰。获取到待过期的 LeaseID 后，Leader 是如何通知其他 Follower 节点淘汰它们呢？Lessor 模块会将已确认过期的 LeaseID，保存在一个名为 expiredC 的 channel 中，而 etcd server 的主循环会定期从 channel 中获取 LeaseID，发起 revoke 请求，通过 Raft Log 传递给 Follower 节点。各个节点收到 revoke Lease 请求后，获取关联到此 Lease 上的 key 列表，从 boltdb 中删除 key，从 Lessor 的 Lease map 内存中删除此 Lease 对象，最后还需要从 boltdb 的 Lease bucket 中删除这个 Lease。以上就是 Lease 的过期自动淘汰逻辑。Leader 节点按过期时间维护了一个最小堆，若你的节点异常未正常续期，那么随着时间消逝，对应的 Lease 则会过期，Lessor 主循环定时轮询过期的 Lease。获取到 ID 后，Leader 发起 revoke 操作，通知整个集群删除 Lease 和关联的数据。

### 为什么需要 checkpoint 机制
了解完 Lease 的创建、续期、自动淘汰机制后，你可能已经发现，检查 Lease 是否过期、维护最小堆、针对过期的 Lease 发起 revoke 操作，都是 Leader 节点负责的，它类似于 Lease 的仲裁者，通过以上清晰的权责划分，降低了 Lease 特性的实现复杂度。那么当 Leader 因重启、crash、磁盘 IO 等异常不可用时，Follower 节点就会发起 Leader 选举，新 Leader 要完成以上职责，必须重建 Lease 过期最小堆等管理数据结构，那么以上重建可能会触发什么问题呢？当你的集群发生 Leader 切换后，新的 Leader 基于 Lease map 信息，按 Lease 过期时间构建一个最小堆时，etcd 早期版本为了优化性能，并未持久化存储 Lease 剩余 TTL 信息，因此重建的时候就会自动给所有 Lease 自动续期了。然而若较频繁出现 Leader 切换，切换时间小于 Lease 的 TTL，这会导致 Lease 永远无法删除，大量 key 堆积，db 大小超过配额等异常。为了解决这个问题，etcd 引入了检查点机制，也就是下面架构图中黑色虚线框所示的 CheckPointScheduledLeases 的任务。

![img](https://static001.geekbang.org/resource/image/70/59/70ece2fa3bc400edd8d3b09f752ea759.png?wh=2580*1560)



一方面，etcd 启动的时候，Leader 节点后台会运行此异步任务，定期批量地将 Lease 剩余的 TTL 基于 Raft Log 同步给 Follower 节点，Follower 节点收到 CheckPoint 请求后，更新内存数据结构 LeaseMap 的剩余 TTL 信息。另一方面，当 Leader 节点收到 KeepAlive 请求的时候，它也会通过 checkpoint 机制把此 Lease 的剩余 TTL 重置，并同步给 Follower 节点，尽量确保续期后集群各个节点的 Lease 剩余 TTL 一致性。最后你要注意的是，此特性对性能有一定影响，目前仍然是试验特性。你可以通过 experimental-enable-lease-checkpoint 参数开启。


### 小结
最后我们来小结下今天的内容，我通过一个实际案例为你解读了 Lease 创建、关联 key、续期、淘汰、checkpoint 机制。Lease 的核心是 TTL，当 Lease 的 TTL 过期时，它会自动删除其关联的 key-value 数据。首先是 Lease 创建及续期。当你创建 Lease 时，etcd 会保存 Lease 信息到 boltdb 的 Lease bucket 中。为了防止 Lease 被淘汰，你需要定期发送 LeaseKeepAlive 请求给 etcd server 续期 Lease，本质是更新 Lease 的到期时间。续期的核心挑战是性能，etcd 经历了从 TTL 属性在 key 上，到独立抽象出 Lease，支持多 key 复用相同 TTL，同时协议从 HTTP/1.x 优化成 gRPC 协议，支持多路连接复用，显著降低了 server 连接数等资源开销。其次是 Lease 的淘汰机制，etcd 的 Lease 淘汰算法经历了从时间复杂度 O(N) 到 O(Log N) 的演进，核心是轮询最小堆的 Lease 是否过期，若过期生成 revoke 请求，它会清理 Lease 和其关联的数据。最后我给你介绍了 Lease 的 checkpoint 机制，它是为了解决 Leader 异常情况下 TTL 自动被续期，可能导致 Lease 永不淘汰的问题而诞生。


## 07 | MVCC：如何实现多版本并发控制？

在01课里，我和你介绍 etcd v2 时，提到过它存在的若干局限，如仅保留最新版本 key-value 数据、丢弃历史版本。而 etcd 核心特性 watch 又依赖历史版本，因此 etcd v2 为了缓解这个问题，会在内存中维护一个较短的全局事件滑动窗口，保留最近的 1000 条变更事件。但是在集群写请求较多等场景下，它依然无法提供可靠的 Watch 机制。那么不可靠的 etcd v2 事件机制，在 etcd v3 中是如何解决的呢？我今天要和你分享的 MVCC（Multiversion concurrency control）机制，正是为解决这个问题而诞生的。MVCC 机制的核心思想是保存一个 key-value 数据的多个历史版本，etcd 基于它不仅实现了可靠的 Watch 机制，避免了 client 频繁发起 List Pod 等 expensive request 操作，保障 etcd 集群稳定性。而且 MVCC 还能以较低的并发控制开销，实现各类隔离级别的事务，保障事务的安全性，是事务特性的基础。希望通过本节课，帮助你搞懂 MVCC 含义和 MVCC 机制下 key-value 数据的更新、查询、删除原理，了解 treeIndex 索引模块、boltdb 模块是如何相互协作，实现保存一个 key-value 数据多个历史版本。


### 什么是 MVCC
首先和你聊聊什么是 MVCC，从名字上理解，它是一个基于多版本技术实现的一种并发控制机制。那常见的并发机制有哪些？MVCC 的优点在哪里呢？提到并发控制机制你可能就没那么陌生了，比如数据库中的悲观锁，也就是通过锁机制确保同一时刻只能有一个事务对数据进行修改操作，常见的实现方案有读写锁、互斥锁、两阶段锁等。悲观锁是一种事先预防机制，它悲观地认为多个并发事务可能会发生冲突，因此它要求事务必须先获得锁，才能进行修改数据操作。但是悲观锁粒度过大、高并发场景下大量事务会阻塞等，会导致服务性能较差。

MVCC 机制正是基于多版本技术实现的一种乐观锁机制，它乐观地认为数据不会发生冲突，但是当事务提交时，具备检测数据是否冲突的能力。在 MVCC 数据库中，你更新一个 key-value 数据的时候，它并不会直接覆盖原数据，而是新增一个版本来存储新的数据，每个数据都有一个版本号。版本号它是一个逻辑时间，为了方便你深入理解版本号意义，在下面我给你画了一个 etcd MVCC 版本号时间序列图。从图中你可以看到，随着时间增长，你每次修改操作，版本号都会递增。每修改一次，生成一条新的数据记录。当你指定版本号读取数据时，它实际上访问的是版本号生成那个时间点的快照数据。当你删除数据的时候，它实际也是新增一条带删除标识的数据记录。

![img](https://static001.geekbang.org/resource/image/1f/2c/1fbf4aa426c8b78570ed310a8c9e2c2c.png?wh=1920*806)


### MVCC 特性初体验
了解完什么是 MVCC 后，我先通过几个简单命令，带你初体验下 MVCC 特性，看看它是如何帮助你查询历史修改记录，以及找回不小心删除的 key 的。启动一个空集群，更新两次 key hello 后，如何获取 key hello 的上一个版本值呢？ 删除 key hello 后，还能读到历史版本吗?如下面的命令所示，第一次 key hello 更新完后，我们通过 get 命令获取下它的 key-value 详细信息。正如你所看到的，除了 key、value 信息，还有各类版本号，我后面会详细和你介绍它们的含义。这里我们重点关注 mod_revision，它表示 key 最后一次修改时的 etcd 版本号。当我们再次更新 key hello 为 world2 后，然后通过查询时指定 key 第一次更新后的版本号，你会发现我们查询到了第一次更新的值，甚至我们执行删除 key hello 后，依然可以获得到这个值。那么 etcd 是如何实现的呢?



```
# 更新key hello为world1
$ etcdctl put hello world1
OK
# 通过指定输出模式为json,查看key hello更新后的详细信息
$ etcdctl get hello -w=json
{
    "kvs":[
        {
            "key":"aGVsbG8=",
            "create_revision":2,
            "mod_revision":2,
            "version":1,
            "value":"d29ybGQx"
        }
    ],
    "count":1
}
# 再次修改key hello为world2
$ etcdctl put hello world2
OK
# 确认修改成功,最新值为wolrd2
$ etcdctl get hello
hello
world2
# 指定查询版本号,获得了hello上一次修改的值
$ etcdctl get hello --rev=2
hello
world1
# 删除key hello
$ etcdctl del  hello
1
# 删除后指定查询版本号3,获得了hello删除前的值
$ etcdctl get hello --rev=3
hello
world2
```

### 整体架构
在详细和你介绍 etcd 如何实现 MVCC 特性前，我先和你从整体上介绍下 MVCC 模块。下图是 MVCC 模块的一个整体架构图，整个 MVCC 特性由 treeIndex、Backend/boltdb 组成。当你执行 MVCC 特性初体验中的 put 命令后，请求经过 gRPC KV Server、Raft 模块流转，对应的日志条目被提交后，Apply 模块开始执行此日志内容。

![img](https://static001.geekbang.org/resource/image/f5/2c/f5799da8d51a381527068a95bb13592c.png?wh=1920*1064)

Apply 模块通过 MVCC 模块来执行 put 请求，持久化 key-value 数据。MVCC 模块将请求请划分成两个类别，分别是读事务（ReadTxn）和写事务（WriteTxn）。读事务负责处理 range 请求，写事务负责 put/delete 操作。读写事务基于 treeIndex、Backend/boltdb 提供的能力，实现对 key-value 的增删改查功能。treeIndex 模块基于内存版 B-tree 实现了 key 索引管理，它保存了用户 key 与版本号（revision）的映射关系等信息。Backend 模块负责 etcd 的 key-value 持久化存储，主要由 ReadTx、BatchTx、Buffer 组成，ReadTx 定义了抽象的读事务接口，BatchTx 在 ReadTx 之上定义了抽象的写事务接口，Buffer 是数据缓存区。etcd 设计上支持多种 Backend 实现，目前实现的 Backend 是 boltdb。boltdb 是一个基于 B+ tree 实现的、支持事务的 key-value 嵌入式数据库。treeIndex 与 boltdb 关系你可参考下图。当你发起一个 get hello 命令时，从 treeIndex 中获取 key 的版本号，然后再通过这个版本号，从 boltdb 获取 value 信息。boltdb 的 value 是包含用户 key-value、各种版本号、lease 信息的结构体。

![img](https://static001.geekbang.org/resource/image/e7/8f/e713636c6cf9c46c7c19f677232d858f.png?wh=1920*903)

接下来我和你重点聊聊 treeIndex 模块的原理与核心数据结构。

### treeIndex 原理
为什么需要 treeIndex 模块呢?对于 etcd v2 来说，当你通过 etcdctl 发起一个 put hello 操作时，etcd v2 直接更新内存树，这就导致历史版本直接被覆盖，无法支持保存 key 的历史版本。在 etcd v3 中引入 treeIndex 模块正是为了解决这个问题，支持保存 key 的历史版本，提供稳定的 Watch 机制和事务隔离等能力。那 etcd v3 又是如何基于 treeIndex 模块，实现保存 key 的历史版本的呢?在 02 节课里，我们提到过 etcd 在每次修改 key 时会生成一个全局递增的版本号（revision），然后通过数据结构 B-tree 保存用户 key 与版本号之间的关系，再以版本号作为 boltdb key，以用户的 key-value 等信息作为 boltdb value，保存到 boltdb。下面我就为你介绍下，etcd 保存用户 key 与版本号映射关系的数据结构 B-tree，为什么 etcd 使用它而不使用哈希表、平衡二叉树？从 etcd 的功能特性上分析， 因 etcd 支持范围查询，因此保存索引的数据结构也必须支持范围查询才行。所以哈希表不适合，而 B-tree 支持范围查询。从性能上分析，平横二叉树每个节点只能容纳一个数据、导致树的高度较高，而 B-tree 每个节点可以容纳多个数据，树的高度更低，更扁平，涉及的查找次数更少，具有优越的增、删、改、查性能。Google 的开源项目 btree，使用 Go 语言实现了一个内存版的 B-tree，对外提供了简单易用的接口。etcd 正是基于 btree 库实现了一个名为 treeIndex 的索引模块，通过它来查询、保存用户 key 与版本号之间的关系。下图是个最大度（degree > 1，简称 d）为 5 的 B-tree，度是 B-tree 中的一个核心参数，它决定了你每个节点上的数据量多少、节点的“胖”、“瘦”程度。从图中你可以看到，节点越胖，意味着一个节点可以存储更多数据，树的高度越低。在一个度为 d 的 B-tree 中，节点保存的最大 key 数为 2d - 1，否则需要进行平衡、分裂操作。这里你要注意的是在 etcd treeIndex 模块中，创建的是最大度 32 的 B-tree，也就是一个叶子节点最多可以保存 63 个 key。

![img](https://static001.geekbang.org/resource/image/44/74/448c8a2bb3b5d2d48dfb6ea585172c74.png?wh=1920*934)

从图中你可以看到，你通过 put/txn 命令写入的一系列 key，treeIndex 模块基于 B-tree 将其组织起来，节点之间基于用户 key 比较大小。当你查找一个 key k95 时，通过 B-tree 的特性，你仅需通过图中流程 1 和 2 两次快速比较，就可快速找到 k95 所在的节点。在 treeIndex 中，每个节点的 key 是一个 keyIndex 结构，etcd 就是通过它保存了用户的 key 与版本号的映射关系。那么 keyIndex 结构包含哪些信息呢？下面是字段说明，你可以参考一下。

```
type keyIndex struct {
   key         []byte //用户的key名称，比如我们案例中的"hello"
   modified    revision //最后一次修改key时的etcd版本号,比如我们案例中的刚写入hello为world1时的，版本号为2
   generations []generation //generation保存了一个key若干代版本号信息，每代中包含对key的多次修改的版本号列表
}
```
keyIndex 中包含用户的 key、最后一次修改 key 时的 etcd 版本号、key 的若干代（generation）版本号信息，每代中包含对 key 的多次修改的版本号列表。那我们要如何理解 generations？为什么它是个数组呢?generations 表示一个 key 从创建到删除的过程，每代对应 key 的一个生命周期的开始与结束。当你第一次创建一个 key 时，会生成第 0 代，后续的修改操作都是在往第 0 代中追加修改版本号。当你把 key 删除后，它就会生成新的第 1 代，一个 key 不断经历创建、删除的过程，它就会生成多个代。generation 结构详细信息如下：

```
type generation struct {
   ver     int64    //表示此key的修改次数
   created revision //表示generation结构创建时的版本号
   revs    []revision //每次修改key时的revision追加到此数组
}
```


generation 结构中包含此 key 的修改次数、generation 创建时的版本号、对此 key 的修改版本号记录列表。你需要注意的是版本号（revision）并不是一个简单的整数，而是一个结构体。revision 结构及含义如下：

```
type revision struct {
   main int64    // 一个全局递增的主版本号，随put/txn/delete事务递增，一个事务内的key main版本号是一致的
   sub int64    // 一个事务内的子版本号，从0开始随事务内put/delete操作递增
}
```

revision 包含 main 和 sub 两个字段，main 是全局递增的版本号，它是个 etcd 逻辑时钟，随着 put/txn/delete 等事务递增。sub 是一个事务内的子版本号，从 0 开始随事务内的 put/delete 操作递增。比如启动一个空集群，全局版本号默认为 1，执行下面的 txn 事务，它包含两次 put、一次 get 操作，那么按照我们上面介绍的原理，全局版本号随读写事务自增，因此是 main 为 2，sub 随事务内的 put/delete 操作递增，因此 key hello 的 revison 为{2,0}，key world 的 revision 为{2,1}。

```
$ etcdctl txn -i
compares:


success requests (get，put，del):
put hello 1
get hello
put world 2
```

介绍完 treeIndex 基本原理、核心数据结构后，我们再看看在 MVCC 特性初体验中的更新、查询、删除 key 案例里，treeIndex 与 boltdb 是如何协作，完成以上 key-value 操作的?


### MVCC 更新 key 原理
当你通过 etcdctl 发起一个 put hello 操作时，如下面的 put 事务流程图流程一所示，在 put 写事务中，首先它需要从 treeIndex 模块中查询 key 的 keyIndex 索引信息，keyIndex 中存储了 key 的创建版本号、修改的次数等信息，这些信息在事务中发挥着重要作用，因此会存储在 boltdb 的 value 中。在我们的案例中，因为是第一次创建 hello key，此时 keyIndex 索引为空。

![img](https://static001.geekbang.org/resource/image/84/e1/84377555cb4150ea7286c9ef3c5e17e1.png?wh=1920*1063)

其次 etcd 会根据当前的全局版本号（空集群启动时默认为 1）自增，生成 put hello 操作对应的版本号 revision{2,0}，这就是 boltdb 的 key。boltdb 的 value 是 mvccpb.KeyValue 结构体，它是由用户 key、value、create_revision、mod_revision、version、lease 组成。它们的含义分别如下：

create_revision 表示此 key 创建时的版本号。在我们的案例中，key hello 是第一次创建，那么值就是 2。当你再次修改 key hello 的时候，写事务会从 treeIndex 模块查询 hello 第一次创建的版本号，也就是 keyIndex.generations[i].created 字段，赋值给 create_revision 字段；mod_revision 表示 key 最后一次修改时的版本号，即 put 操作发生时的全局版本号加 1；version 表示此 key 的修改次数。每次修改的时候，写事务会从 treeIndex 模块查询 hello 已经历过的修改次数，也就是 keyIndex.generations[i].ver 字段，将 ver 字段值加 1 后，赋值给 version 字段。

填充好 boltdb 的 KeyValue 结构体后，这时就可以通过 Backend 的写事务 batchTx 接口将 key{2,0},value 为 mvccpb.KeyValue 保存到 boltdb 的缓存中，并同步更新 buffer，如上图中的流程二所示。此时存储到 boltdb 中的 key、value 数据如下：

![img](https://static001.geekbang.org/resource/image/a2/ba/a245b18eabc86ea83a71349f49bdceba.jpg?wh=1807*397)

然后 put 事务需将本次修改的版本号与用户 key 的映射关系保存到 treeIndex 模块中，也就是上图中的流程三。因为 key hello 是首次创建，treeIndex 模块它会生成 key hello 对应的 keyIndex 对象，并填充相关数据结构。keyIndex 填充后的结果如下所示：

```
key hello的keyIndex:
key:     "hello"
modified: <2,0>
generations:
[{ver:1,created:<2,0>,revisions: [<2,0>]} ]
```

我们来简易分析一下上面的结果。key 为 hello，modified 为最后一次修改版本号 <2,0>，key hello 是首次创建的，因此新增一个 generation 代跟踪它的生命周期、修改记录；generation 的 ver 表示修改次数，首次创建为 1，后续随着修改操作递增；generation.created 表示创建 generation 时的版本号为 <2,0>；revision 数组保存对此 key 修改的版本号列表，每次修改都会将将相应的版本号追加到 revisions 数组中。


通过以上流程，一个 put 操作终于完成。但是此时数据还并未持久化，为了提升 etcd 的写吞吐量、性能，一般情况下（默认堆积的写事务数大于 1 万才在写事务结束时同步持久化），数据持久化由 Backend 的异步 goroutine 完成，它通过事务批量提交，定时将 boltdb 页缓存中的脏数据提交到持久化存储磁盘中，也就是下图中的黑色虚线框住的流程四。

![img](https://static001.geekbang.org/resource/image/5d/a2/5de49651cedf4595648aeba3c131cea2.png?wh=1920*1059)


### MVCC 查询 key 原理
完成 put hello 为 world1 操作后，这时你通过 etcdctl 发起一个 get hello 操作，MVCC 模块首先会创建一个读事务对象（TxnRead），在 etcd 3.4 中 Backend 实现了 ConcurrentReadTx， 也就是并发读特性。并发读特性的核心原理是创建读事务对象时，它会全量拷贝当前写事务未提交的 buffer 数据，并发的读写事务不再阻塞在一个 buffer 资源锁上，实现了全并发读。

![img](https://static001.geekbang.org/resource/image/55/ee/55998d8a1f3091076a9119d85e7175ee.png?wh=1920*1070)



如上图所示，在读事务中，它首先需要根据 key 从 treeIndex 模块获取版本号，因我们未带版本号读，默认是读取最新的数据。treeIndex 模块从 B-tree 中，根据 key 查找到 keyIndex 对象后，匹配有效的 generation，返回 generation 的 revisions 数组中最后一个版本号{2,0}给读事务对象。读事务对象根据此版本号为 key，通过 Backend 的并发读事务（ConcurrentReadTx）接口，优先从 buffer 中查询，命中则直接返回，否则从 boltdb 中查询此 key 的 value 信息。那指定版本号读取历史记录又是怎么实现的呢？

当你再次发起一个 put hello 为 world2 修改操作时，key hello 对应的 keyIndex 的结果如下面所示，keyIndex.modified 字段更新为 <3,0>，generation 的 revision 数组追加最新的版本号 <3,0>，ver 修改为 2。


```
key hello的keyIndex:
key:     "hello"
modified: <3,0>
generations:
[{ver:2,created:<2,0>,revisions: [<2,0>,<3,0>]}]
```
boltdb 插入一个新的 key revision{3,0}，此时存储到 boltdb 中的 key-value 数据如下：

![img](https://static001.geekbang.org/resource/image/8b/f7/8bec06d61622f2a99ea9dd2f78e693f7.jpg?wh=1432*477)

这时你再发起一个指定历史版本号为 2 的读请求时，实际是读版本号为 2 的时间点的快照数据。treeIndex 模块会遍历 generation 内的历史版本号，返回小于等于 2 的最大历史版本号，在我们这个案例中，也就是 revision{2,0}，以它作为 boltdb 的 key，从 boltdb 中查询出 value 即可。

### MVCC 删除 key 原理
介绍完 MVCC 更新、查询 key 的原理后，我们接着往下看。当你执行 etcdctl del hello 命令时，etcd 会立刻从 treeIndex 和 boltdb 中删除此数据吗？还是增加一个标记实现延迟删除（lazy delete）呢？答案为 etcd 实现的是延期删除模式，原理与 key 更新类似。与更新 key 不一样之处在于，一方面，生成的 boltdb key 版本号{4,0,t}追加了删除标识（tombstone, 简写 t），boltdb value 变成只含用户 key 的 KeyValue 结构体。另一方面 treeIndex 模块也会给此 key hello 对应的 keyIndex 对象，追加一个空的 generation 对象，表示此索引对应的 key 被删除了。当你再次查询 hello 的时候，treeIndex 模块根据 key hello 查找到 keyindex 对象后，若发现其存在空的 generation 对象，并且查询的版本号大于等于被删除时的版本号，则会返回空。etcdctl hello 操作后的 keyIndex 的结果如下面所示：

```
key hello的keyIndex:
key:     "hello"
modified: <4,0>
generations:
[
{ver:3,created:<2,0>,revisions: [<2,0>,<3,0>,<4,0>(t)]}，             
{empty}
]
```
boltdb 此时会插入一个新的 key revision{4,0,t}，此时存储到 boltdb 中的 key-value 数据如下：

![img](https://static001.geekbang.org/resource/image/da/17/da4e5bc5033619dda296c022ac6yyc17.jpg?wh=1611*581)

那么 key 打上删除标记后有哪些用途呢？什么时候会真正删除它呢？一方面删除 key 时会生成 events，Watch 模块根据 key 的删除标识，会生成对应的 Delete 事件。另一方面，当你重启 etcd，遍历 boltdb 中的 key 构建 treeIndex 内存树时，你需要知道哪些 key 是已经被删除的，并为对应的 key 索引生成 tombstone 标识。而真正删除 treeIndex 中的索引对象、boltdb 中的 key 是通过压缩 (compactor) 组件异步完成。正因为 etcd 的删除 key 操作是基于以上延期删除原理实现的，因此只要压缩组件未回收历史版本，我们就能从 etcd 中找回误删的数据。


### 小结
最后我们来小结下今天的内容，我通过 MVCC 特性初体验中的更新、查询、删除 key 案例，为你分析了 MVCC 整体架构、核心模块，它由 treeIndex、boltdb 组成。treeIndex 模块基于 Google 开源的 btree 库实现，它的核心数据结构 keyIndex，保存了用户 key 与版本号关系。每次修改 key 都会生成新的版本号，生成新的 boltdb key-value。boltdb 的 key 为版本号，value 包含用户 key-value、各种版本号、lease 的 mvccpb.KeyValue 结构体。当你未带版本号查询 key 时，etcd 返回的是 key 最新版本数据。当你指定版本号读取数据时，etcd 实际上返回的是版本号生成那个时间点的快照数据。删除一个数据时，etcd 并未真正删除它，而是基于 lazy delete 实现的异步删除。删除原理本质上与更新操作类似，只不过 boltdb 的 key 会打上删除标记，keyIndex 索引中追加空的 generation。真正删除 key 是通过 etcd 的压缩组件去异步实现的，在后面的课程里我会继续和你深入介绍。基于以上原理特性的实现，etcd 实现了保存 key 历史版本的功能，是高可靠 Watch 机制的基础。基于 key-value 中的各种版本号信息，etcd 可提供各种级别的简易事务隔离能力。基于 Backend/boltdb 提供的 MVCC 机制，etcd 可实现读写不冲突。

## 08 | Watch：如何高效获取数据变化通知？

在 Kubernetes 中，各种各样的控制器实现了 Deployment、StatefulSet、Job 等功能强大的 Workload。控制器的核心思想是监听、比较资源实际状态与期望状态是否一致，若不一致则进行协调工作，使其最终一致。那么当你修改一个 Deployment 的镜像时，Deployment 控制器是如何高效的感知到期望状态发生了变化呢？要回答这个问题，得从 etcd 的 Watch 特性说起，它是 Kubernetes 控制器的工作基础。今天我和你分享的主题就是 etcd 的核心特性 Watch 机制设计实现，通过分析 Watch 机制的四大核心问题，让你了解一个变化数据是如何从 0 到 1 推送给 client，并给你介绍 Watch 特性从 etcd v2 到 etcd v3 演进、优化过程。希望通过这节课，你能在实际业务中应用 Watch 特性，快速获取数据变更通知，而不是使用可能导致大量 expensive request 的轮询模式。更进一步，我将帮助你掌握 Watch 过程中，可能会出现的各种异常错误和原因，并知道在业务中如何优雅处理，让你的服务更稳地运行。

### Watch 特性初体验
在详细介绍 Watch 特性实现原理之前，我先通过几个简单命令，带你初体验下 Watch 特性。启动一个空集群，更新两次 key hello 后，使用 Watch 特性如何获取 key hello 的历史修改记录呢？如下所示，你可以通过下面的 watch 命令，带版本号监听 key hello，集群版本号可通过 endpoint status 命令获取，空集群启动后的版本号为 1。执行后输出如下代码所示，两个事件记录分别对应上面的两次的修改，事件中含有 key、value、各类版本号等信息，你还可以通过比较 create_revision 和 mod_revision 区分此事件是 add 还是 update 事件。watch 命令执行后，你后续执行的增量 put hello 修改操作，它同样可持续输出最新的变更事件给你。

```
$ etcdctl put hello world1
$ etcdctl put hello world2
$ etcdctl watch hello -w=json --rev=1
{
    "Events":[
        {
            "kv":{
                "key":"aGVsbG8=",
                "create_revision":2,
                "mod_revision":2,
                "version":1,
                "value":"d29ybGQx"
            }
        },
        {
            "kv":{
                "key":"aGVsbG8=",
                "create_revision":2,
                "mod_revision":3,
                "version":2,
                "value":"d29ybGQy"
            }
        }
    ],
    "CompactRevision":0,
    "Canceled":false,
    "Created":false
}
```
从以上初体验中，你可以看到，基于 Watch 特性，你可以快速获取到你感兴趣的数据变化事件，这也是 Kubernetes 控制器工作的核心基础。在这过程中，其实有以下四大核心问题：第一，client 获取事件的机制，etcd 是使用轮询模式还是推送模式呢？两者各有什么优缺点？第二，事件是如何存储的？ 会保留多久？watch 命令中的版本号具有什么作用？第三，当 client 和 server 端出现短暂网络波动等异常因素后，导致事件堆积时，server 端会丢弃事件吗？若你监听的历史版本号 server 端不存在了，你的代码该如何处理？第四，如果你创建了上万个 watcher 监听 key 变化，当 server 端收到一个写请求后，etcd 是如何根据变化的 key 快速找到监听它的 watcher 呢？接下来我就和你分别详细聊聊 etcd Watch 特性是如何解决这四大问题的。搞懂这四个问题，你就明白 etcd 甚至各类分布式存储 Watch 特性的核心实现原理了。

### 轮询 vs 流式推送
首先第一个问题是 client 获取事件机制，etcd 是使用轮询模式还是推送模式呢？两者各有什么优缺点？答案是两种机制 etcd 都使用过。在 etcd v2 Watch 机制实现中，使用的是 HTTP/1.x 协议，实现简单、兼容性好，每个 watcher 对应一个 TCP 连接。client 通过 HTTP/1.1 协议长连接定时轮询 server，获取最新的数据变化事件。然而当你的 watcher 成千上万的时，即使集群空负载，大量轮询也会产生一定的 QPS，server 端会消耗大量的 socket、内存等资源，导致 etcd 的扩展性、稳定性无法满足 Kubernetes 等业务场景诉求。etcd v3 的 Watch 机制的设计实现并非凭空出现，它正是吸取了 etcd v2 的经验、教训而重构诞生的。在 etcd v3 中，为了解决 etcd v2 的以上缺陷，使用的是基于 HTTP/2 的 gRPC 协议，双向流的 Watch API 设计，实现了连接多路复用。HTTP/2 协议为什么能实现多路复用呢？

![img](https://static001.geekbang.org/resource/image/be/74/be3a019beaf1310d214e5c9948cc9c74.png?wh=1784*534)

在 HTTP/2 协议中，HTTP 消息被分解独立的帧（Frame），交错发送，帧是最小的数据单位。每个帧会标识属于哪个流（Stream），流由多个数据帧组成，每个流拥有一个唯一的 ID，一个数据流对应一个请求或响应包。如上图所示，client 正在向 server 发送数据流 5 的帧，同时 server 也正在向 client 发送数据流 1 和数据流 3 的一系列帧。一个连接上有并行的三个数据流，HTTP/2 可基于帧的流 ID 将并行、交错发送的帧重新组装成完整的消息。通过以上机制，HTTP/2 就解决了 HTTP/1 的请求阻塞、连接无法复用的问题，实现了多路复用、乱序发送。etcd 基于以上介绍的 HTTP/2 协议的多路复用等机制，实现了一个 client/TCP 连接支持多 gRPC Stream， 一个 gRPC Stream 又支持多个 watcher，如下图所示。同时事件通知模式也从 client 轮询优化成 server 流式推送，极大降低了 server 端 socket、内存等资源。

![img](https://static001.geekbang.org/resource/image/f0/be/f08d1c50c6bc14f09b5028095ce275be.png?wh=1804*1076)

当然在 etcd v3 watch 性能优化的背后，也带来了 Watch API 复杂度上升, 不过你不用担心，etcd 的 clientv3 库已经帮助你搞定这些棘手的工作了。在 clientv3 库中，Watch 特性被抽象成 Watch、Close、RequestProgress 三个简单 API 提供给开发者使用，屏蔽了 client 与 gRPC WatchServer 交互的复杂细节，实现了一个 client 支持多个 gRPC Stream，一个 gRPC Stream 支持多个 watcher，显著降低了你的开发复杂度。同时当 watch 连接的节点故障，clientv3 库支持自动重连到健康节点，并使用之前已接收的最大版本号创建新的 watcher，避免旧事件回放等。

### 滑动窗口 vs MVCC
介绍完 etcd v2 的轮询机制和 etcd v3 的流式推送机制后，再看第二个问题，事件是如何存储的？ 会保留多久呢？watch 命令中的版本号具有什么作用？第二个问题的本质是历史版本存储，etcd 经历了从滑动窗口到 MVCC 机制的演变，滑动窗口是仅保存有限的最近历史版本到内存中，而 MVCC 机制则将历史版本保存在磁盘中，避免了历史版本的丢失，极大的提升了 Watch 机制的可靠性。etcd v2 滑动窗口是如何实现的？它有什么缺点呢？它使用的是如下一个简单的环形数组来存储历史事件版本，当 key 被修改后，相关事件就会被添加到数组中来。若超过 eventQueue 的容量，则淘汰最旧的事件。在 etcd v2 中，eventQueue 的容量是固定的 1000，因此它最多只会保存 1000 条事件记录，不会占用大量 etcd 内存导致 etcd OOM。

```
type EventHistory struct {
   Queue      eventQueue
   StartIndex uint64
   LastIndex  uint64
   rwl        sync.RWMutex
}
```

但是它的缺陷显而易见的，固定的事件窗口只能保存有限的历史事件版本，是不可靠的。当写请求较多的时候、client 与 server 网络出现波动等异常时，很容易导致事件丢失，client 不得不触发大量的 expensive 查询操作，以获取最新的数据及版本号，才能持续监听数据。特别是对于重度依赖 Watch 机制的 Kubernetes 来说，显然是无法接受的。因为这会导致控制器等组件频繁的发起 expensive List Pod 等资源操作，导致 APIServer/etcd 出现高负载、OOM 等，对稳定性造成极大的伤害。etcd v3 的 MVCC 机制，正如上一节课所介绍的，就是为解决 etcd v2 Watch 机制不可靠而诞生。相比 etcd v2 直接保存事件到内存的环形数组中，etcd v3 则是将一个 key 的历史修改版本保存在 boltdb 里面。boltdb 是一个基于磁盘文件的持久化存储，因此它重启后历史事件不像 etcd v2 一样会丢失，同时你可通过配置压缩策略，来控制保存的历史版本数，在压缩篇我会和你详细讨论它。最后 watch 命令中的版本号具有什么作用呢?在上一节课中我们深入介绍了它的含义，版本号是 etcd 逻辑时钟，当 client 因网络等异常出现连接闪断后，通过版本号，它就可从 server 端的 boltdb 中获取错过的历史事件，而无需全量同步，它是 etcd Watch 机制数据增量同步的核心。
### 可靠的事件推送机制
再看第三个问题，当 client 和 server 端出现短暂网络波动等异常因素后，导致事件堆积时，server 端会丢弃事件吗？若你监听的历史版本号 server 端不存在了，你的代码该如何处理？第三个问题的本质是可靠事件推送机制，要搞懂它，我们就得弄懂 etcd Watch 特性的整体架构、核心流程，下图是 Watch 特性整体架构图。

整体架构

![img](https://static001.geekbang.org/resource/image/42/bf/42575d8d0a034e823b8e48d4ca0a49bf.png?wh=1920*1075)

我先通过上面的架构图，给你简要介绍下一个 watch 请求流程，让你对全流程有个整体的认识。当你通过 etcdctl 或 API 发起一个 watch key 请求的时候，etcd 的 gRPCWatchServer 收到 watch 请求后，会创建一个 serverWatchStream, 它负责接收 client 的 gRPC Stream 的 create/cancel watcher 请求 (recvLoop goroutine)，并将从 MVCC 模块接收的 Watch 事件转发给 client(sendLoop goroutine)。当 serverWatchStream 收到 create watcher 请求后，serverWatchStream 会调用 MVCC 模块的 WatchStream 子模块分配一个 watcher id，并将 watcher 注册到 MVCC 的 WatchableKV 模块。在 etcd 启动的时候，WatchableKV 模块会运行 syncWatchersLoop 和 syncVictimsLoop goroutine，分别负责不同场景下的事件推送，它们也是 Watch 特性可靠性的核心之一。从架构图中你可以看到 Watch 特性的核心实现是 WatchableKV 模块，下面我就为你抽丝剥茧，看看"etcdctl watch hello -w=json --rev=1"命令在 WatchableKV 模块是如何处理的？面对各类异常，它如何实现可靠事件推送？



**etcd 核心解决方案是复杂度管理，问题拆分。**

etcd 根据不同场景，对问题进行了分解，将 watcher 按场景分类，实现了轻重分离、低耦合。我首先给你介绍下 synced watcher、unsynced watcher 它们各自的含义。synced watcher，顾名思义，表示此类 watcher 监听的数据都已经同步完毕，在等待新的变更。如果你创建的 watcher 未指定版本号 (默认 0)、或指定的版本号大于 etcd sever 当前最新的版本号 (currentRev)，那么它就会保存到 synced watcherGroup 中。watcherGroup 负责管理多个 watcher，能够根据 key 快速找到监听该 key 的一个或多个 watcher。unsynced watcher，表示此类 watcher 监听的数据还未同步完成，落后于当前最新数据变更，正在努力追赶。如果你创建的 watcher 指定版本号小于 etcd server 当前最新版本号，那么它就会保存到 unsynced watcherGroup 中。比如我们的这个案例中 watch 带指定版本号 1 监听时，版本号 1 和 etcd server 当前版本之间的数据并未同步给你，因此它就属于此类。从以上介绍中，我们可以将可靠的事件推送机制拆分成最新事件推送、异常场景重试、历史事件推送机制三个子问题来进行分析。下面是第一个子问题，最新事件推送机制。


### 最新事件推送机制
当 etcd 收到一个写请求，key-value 发生变化的时候，处于 syncedGroup 中的 watcher，是如何获取到最新变化事件并推送给 client 的呢？

![img](https://static001.geekbang.org/resource/image/5y/48/5yy0cbf2833c438812086287d2ebf948.png?wh=1920*1060)

当你创建完成 watcher 后，此时你执行 put hello 修改操作时，如上图所示，请求经过 KVServer、Raft 模块后 Apply 到状态机时，在 MVCC 的 put 事务中，它会将本次修改的后的 mvccpb.KeyValue 保存到一个 changes 数组中。在 put 事务结束时，如下面的精简代码所示，它会将 KeyValue 转换成 Event 事件，然后回调 watchableStore.notify 函数（流程 5）。notify 会匹配出监听过此 key 并处于 synced watcherGroup 中的 watcher，同时事件中的版本号要大于等于 watcher 监听的最小版本号，才能将事件发送到此 watcher 的事件 channel 中。serverWatchStream 的 sendLoop goroutine 监听到 channel 消息后，读出消息立即推送给 client（流程 6 和 7），至此，完成一个最新修改事件推送。


```
evs := make([]mvccpb.Event, len(changes))
for i, change := range changes {
   evs[i].Kv = &changes[i]
   if change.CreateRevision == 0 {
      evs[i].Type = mvccpb.DELETE
      evs[i].Kv.ModRevision = rev
   } else {
      evs[i].Type = mvccpb.PUT
   }
}
tw.s.notify(rev, evs)
```
注意接收 Watch 事件 channel 的 buffer 容量默认 1024(etcd v3.4.9)。若 client 与 server 端因网络波动、高负载等原因导致推送缓慢，buffer 满了，事件会丢失吗？这就是第二个子问题，异常场景的重试机制。


### 异常场景重试机制
若出现 channel buffer 满了，etcd 为了保证 Watch 事件的高可靠性，并不会丢弃它，而是将此 watcher 从 synced watcherGroup 中删除，然后将此 watcher 和事件列表保存到一个名为受害者 victim 的 watcherBatch 结构中，通过异步机制重试保证事件的可靠性。还有一个点你需要注意的是，notify 操作它是在修改事务结束时同步调用的，必须是轻量级、高性能、无阻塞的，否则会严重影响集群写性能。那么若因网络波动、CPU 高负载等异常导致 watcher 处于 victim 集合中后，etcd 是如何处理这种 slow watcher 呢？在介绍 Watch 机制整体架构时，我们知道 WatchableKV 模块会启动两个异步 goroutine，其中一个是 syncVictimsLoop，正是它负责 slower watcher 的堆积的事件推送。它的基本工作原理是，遍历 victim watcherBatch 数据结构，尝试将堆积的事件再次推送到 watcher 的接收 channel 中。若推送失败，则再次加入到 victim watcherBatch 数据结构中等待下次重试。若推送成功，watcher 监听的最小版本号 (minRev) 小于等于 server 当前版本号 (currentRev)，说明可能还有历史事件未推送，需加入到 unsynced watcherGroup 中，由下面介绍的历史事件推送机制，推送 minRev 到 currentRev 之间的事件。若 watcher 的最小版本号大于 server 当前版本号，则加入到 synced watcher 集合中，进入上面介绍的最新事件通知机制。下面我给你画了一幅图总结各类 watcher 状态转换关系，希望能帮助你快速厘清之间关系。

![img](https://static001.geekbang.org/resource/image/40/8e/40ec1087113edfc9f7yy0f32394b948e.png?wh=1920*1065)

介绍完最新事件推送、异常场景重试机制后，那历史事件推送机制又是怎么工作的呢？


### 历史事件推送机制
WatchableKV 模块的另一个 goroutine，syncWatchersLoop，正是负责 unsynced watcherGroup 中的 watcher 历史事件推送。在历史事件推送机制中，如果你监听老的版本号已经被 etcd 压缩了，client 该如何处理？要了解这个问题，我们就得搞清楚 syncWatchersLoop 如何工作，它的核心支撑是 boltdb 中存储了 key-value 的历史版本。syncWatchersLoop，它会遍历处于 unsynced watcherGroup 中的每个 watcher，为了优化性能，它会选择一批 unsynced watcher 批量同步，找出这一批 unsynced watcher 中监听的最小版本号。因 boltdb 的 key 是按版本号存储的，因此可通过指定查询的 key 范围的最小版本号作为开始区间，当前 server 最大版本号作为结束区间，遍历 boltdb 获得所有历史数据。然后将 KeyValue 结构转换成事件，匹配出监听过事件中 key 的 watcher 后，将事件发送给对应的 watcher 事件接收 channel 即可。发送完成后，watcher 从 unsynced watcherGroup 中移除、添加到 synced watcherGroup 中，如下面的 watcher 状态转换图黑色虚线框所示。

![img](https://static001.geekbang.org/resource/image/a7/b4/a7a04846de2be66f1162af8845b13ab4.png?wh=1920*1098)

若 watcher 监听的版本号已经小于当前 etcd server 压缩的版本号，历史变更数据就可能已丢失，因此 etcd server 会返回 ErrCompacted 错误给 client。client 收到此错误后，需重新获取数据最新版本号后，再次 Watch。你在业务开发过程中，使用 Watch API 最常见的一个错误之一就是未处理此错误。

### 高效的事件匹配
介绍完可靠的事件推送机制后，最后我们再看第四个问题，如果你创建了上万个 watcher 监听 key 变化，当 server 端收到一个写请求后，etcd 是如何根据变化的 key 快速找到监听它的 watcher 呢？一个个遍历 watcher 吗？显然一个个遍历 watcher 是最简单的方法，但是它的时间复杂度是 O(N)，在 watcher 数较多的场景下，会导致性能出现瓶颈。更何况 etcd 是在执行一个写事务结束时，同步触发事件通知流程的，若匹配 watcher 开销较大，将严重影响 etcd 性能。那使用什么数据结构来快速查找哪些 watcher 监听了一个事件中的 key 呢？也许你会说使用 map 记录下哪些 watcher 监听了什么 key 不就可以了吗？ etcd 的确使用 map 记录了监听单个 key 的 watcher，但是你要注意的是 Watch 特性不仅仅可以监听单 key，它还可以指定监听 key 范围、key 前缀，因此 etcd 还使用了如下的区间树。

![img](https://static001.geekbang.org/resource/image/5a/88/5ae0a99629021e4a05c08yyd0df92f88.png?wh=1920*1040)

当收到创建 watcher 请求的时候，它会把 watcher 监听的 key 范围插入到上面的区间树中，区间的值保存了监听同样 key 范围的 watcher 集合 /watcherSet。当产生一个事件时，etcd 首先需要从 map 查找是否有 watcher 监听了单 key，其次它还需要从区间树找出与此 key 相交的所有区间，然后从区间的值获取监听的 watcher 集合。区间树支持快速查找一个 key 是否在某个区间内，时间复杂度 O(LogN)，因此 etcd 基于 map 和区间树实现了 watcher 与事件快速匹配，具备良好的扩展性。


### 小结
最后我们来小结今天的内容，我通过一个 Watch 特性初体验，提出了 Watch 特性设计实现的四个核心问题，分别是获取事件机制、事件历史版本存储、如何实现可靠的事件推送机制、如何高效的将事件与 watcher 进行匹配。在获取事件机制、事件历史版本存储两个问题中，我给你介绍了 etcd v2 在使用 HTTP/1.x 轮询、滑动窗口时，存在大量的连接数、丢事件等问题，导致扩展性、稳定性较差。而 etcd v3 Watch 特性优化思路是基于 HTTP/2 的流式传输、多路复用，实现了一个连接支持多个 watcher，减少了大量连接数，事件存储也从滑动窗口优化成稳定可靠的 MVCC 机制，历史版本保存在磁盘中，具备更好的扩展性、稳定性。在实现可靠的事件推送机制问题中，我通过一个整体架构图带你了解整个 Watch 机制的核心链路，数据推送流程。Watch 特性的核心实现模块是 watchableStore，它通过将 watcher 划分为 synced/unsynced/victim 三类，将问题进行了分解，并通过多个后台异步循环 goroutine 负责不同场景下的事件推送，提供了各类异常等场景下的 Watch 事件重试机制，尽力确保变更事件不丢失、按逻辑时钟版本号顺序推送给 client。最后一个事件匹配性能问题，etcd 基于 map 和区间树数实现了 watcher 与事件快速匹配，保障了大规模场景下的 Watch 机制性能和读写稳定性。


## 09 | 事务：如何安全地实现多key操作？

在软件开发过程中，我们经常会遇到需要批量执行多个 key 操作的业务场景，比如转账案例中，Alice 给 Bob 转账 100 元，Alice 账号减少 100，Bob 账号增加 100，这涉及到多个 key 的原子更新。无论发生任何故障，我们应用层期望的结果是，要么两个操作一起成功，要么两个一起失败。我们无法容忍出现一个成功，一个失败的情况。那么 etcd 是如何解决多 key 原子更新问题呢？这正是我今天要和你分享的主题——事务，它就是为了简化应用层的编程模型而诞生的。我将通过转账案例为你剖析 etcd 事务实现，让你了解 etcd 如何实现事务 ACID 特性的，以及 MVCC 版本号在事务中的重要作用。希望通过本节课，帮助你在业务开发中正确使用事务，保证软件代码的正确性。


### 事务特性初体验及 API
如何使用 etcd 实现 Alice 向 Bob 转账功能呢？在 etcd v2 的时候， etcd 提供了 CAS（Compare and swap），然而其只支持单 key，不支持多 key，因此无法满足类似转账场景的需求。严格意义上说 CAS 称不上事务，无法实现事务的各个隔离级别。etcd v3 为了解决多 key 的原子操作问题，提供了全新迷你事务 API，同时基于 MVCC 版本号，它可以实现各种隔离级别的事务。它的基本结构如下：
```
client.Txn(ctx).If(cmp1, cmp2, ...).Then(op1, op2, ...,).Else(op1, op2, …)
```
从上面结构中你可以看到，事务 API 由 If 语句、Then 语句、Else 语句组成，这与我们平时常见的 MySQL 事务完全不一样。它的基本原理是，在 If 语句中，你可以添加一系列的条件表达式，若条件表达式全部通过检查，则执行 Then 语句的 get/put/delete 等操作，否则执行 Else 的 get/put/delete 等操作。

那么 If 语句支持哪些检查项呢？

首先是 key 的最近一次修改版本号 mod_revision，简称 mod。你可以通过它检查 key 最近一次被修改时的版本号是否符合你的预期。比如当你查询到 Alice 账号资金为 100 元时，它的 mod_revision 是 v1，当你发起转账操作时，你得确保 Alice 账号上的 100 元未被挪用，这就可以通过 mod(“Alice”) = “v1” 条件表达式来保障转账安全性。其次是 key 的创建版本号 create_revision，简称 create。你可以通过它检查 key 是否已存在。比如在分布式锁场景里，只有分布式锁 key(lock) 不存在的时候，你才能发起 put 操作创建锁，这时你可以通过 create(“lock”) = "0"来判断，因为一个 key 不存在的话它的 create_revision 版本号就是 0。接着是 key 的修改次数 version。你可以通过它检查 key 的修改次数是否符合预期。比如你期望 key 在修改次数小于 3 时，才能发起某些操作时，可以通过 version(“key”) < "3"来判断。最后是 key 的 value 值。你可以通过检查 key 的 value 值是否符合预期，然后发起某些操作。比如期望 Alice 的账号资金为 200, value(“Alice”) = “200”。

If 语句通过以上 MVCC 版本号、value 值、各种比较运算符 (等于、大于、小于、不等于)，实现了灵活的比较的功能，满足你各类业务场景诉求。下面我给出了一个使用 etcdctl 的 txn 事务命令，基于以上介绍的特性，初步实现的一个 Alice 向 Bob 转账 100 元的事务。Alice 和 Bob 初始账上资金分别都为 200 元，事务首先判断 Alice 账号资金是否为 200，若是则执行转账操作，不是则返回最新资金。etcd 是如何执行这个事务的呢？这个事务实现上有哪些问题呢？

```
$ etcdctl txn -i
compares: //对应If语句
value("Alice") = "200" //判断Alice账号资金是否为200


success requests (get, put, del): //对应Then语句
put Alice 100 //Alice账号初始资金200减100
put Bob 300 //Bob账号初始资金200加100


failure requests (get, put, del): //对应Else语句
get Alice  
get Bob


SUCCESS


OK

OK
```


整体流程

![img](https://static001.geekbang.org/resource/image/e4/d3/e41a4f83bda29599efcf06f6012b0bd3.png?wh=1920*852)

在和你介绍上面案例中的 etcd 事务原理和问题前，我先给你介绍下事务的整体流程，为我们后面介绍 etcd 事务 ACID 特性的实现做准备。上图是 etcd 事务的执行流程，当你通过 client 发起一个 txn 转账事务操作时，通过 gRPC KV Server、Raft 模块处理后，在 Apply 模块执行此事务的时候，它首先对你的事务的 If 语句进行检查，也就是 ApplyCompares 操作，如果通过此操作，则执行 ApplyTxn/Then 语句，否则执行 ApplyTxn/Else 语句。在执行以上操作过程中，它会根据事务是否只读、可写，通过 MVCC 层的读写事务对象，执行事务中的 get/put/delete 各操作，也就是我们上一节课介绍的 MVCC 对 key 的读写原理。


### 事务 ACID 特性
了解完事务的整体执行流程后，那么 etcd 应该如何正确实现上面案例中 Alice 向 Bob 转账的事务呢？别着急，我们先来了解一下事务的 ACID 特性。在你了解了 etcd 事务 ACID 特性实现后，这个转账事务案例的正确解决方案也就简单了。ACID 是衡量事务的四个特性，由原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）组成。接下来我就为你分析 ACID 特性在 etcd 中的实现。

### 原子性与持久性
事务的原子性（Atomicity）是指在一个事务中，所有请求要么同时成功，要么同时失败。比如在我们的转账案例中，是绝对无法容忍 Alice 账号扣款成功，但是 Bob 账号资金到账失败的场景。持久性（Durability）是指事务一旦提交，其所做的修改会永久保存在数据库。软件系统在运行过程中会遇到各种各样的软硬件故障，如果 etcd 在执行上面事务过程中，刚执行完扣款命令（put Alice 100）就突然 crash 了，它是如何保证转账事务的原子性与持久性的呢？

![img](https://static001.geekbang.org/resource/image/cf/9e/cf94ce8fc0649fe5cce45f8b7468019e.png?wh=1920*949)

如上图转账事务流程图所示，etcd 在执行一个事务过程中，任何时间点都可能会出现节点 crash 等异常问题。我在图中给你标注了两个关键的异常时间点，它们分别是 T1 和 T2。接下来我分别为你分析一下 etcd 在这两个关键时间点异常后，是如何保证事务的原子性和持久性的。

### T1 时间点
T1 时间点是在 Alice 账号扣款 100 元完成时，Bob 账号资金还未成功增加时突然发生了 crash。从前面介绍的 etcd 写原理和上面流程图我们可知，此时 MVCC 写事务持有 boltdb 写锁，仅是将修改提交到了内存中，保证幂等性、防止日志条目重复执行的一致性索引 consistent index 也并未更新。同时，负责 boltdb 事务提交的 goroutine 因无法持有写锁，也并未将事务提交到持久化存储中。因此，T1 时间点发生 crash 异常后，事务并未成功执行和持久化任意数据到磁盘上。在节点重启时，etcd  server 会重放 WAL 中的已提交日志条目，再次执行以上转账事务。因此不会出现 Alice 扣款成功、Bob 到帐失败等严重 Bug，极大简化了业务的编程复杂度。


### T2 时间点
T2 时间点是在 MVCC 写事务完成转账，server 返回给 client 转账成功后，boltdb 的事务提交 goroutine，批量将事务持久化到磁盘中时发生了 crash。这时 etcd 又是如何保证原子性和持久性的呢?我们知道一致性索引 consistent index 字段值是和 key-value 数据在一个 boltdb 事务里同时持久化到磁盘中的。若在 boltdb 事务提交过程中发生 crash 了，简单情况是 consistent index 和 key-value 数据都更新失败。那么当节点重启，etcd  server 重放 WAL 中已提交日志条目时，同样会再次应用转账事务到状态机中，因此事务的原子性和持久化依然能得到保证。更复杂的情况是，当 boltdb 提交事务的时候，会不会部分数据提交成功，部分数据提交失败呢？这个问题，我将在下一节课通过深入介绍 boltdb 为你解答。了解完 etcd 事务的原子性和持久性后，那一致性又是怎么一回事呢？事务的一致性难道是指各个节点数据一致性吗？

### 一致性
在软件系统中，到处可见一致性（Consistency）的表述，其实在不同场景下，它的含义是不一样的。首先分布式系统中多副本数据一致性，它是指各个副本之间的数据是否一致，比如 Redis 的主备是异步复制的，那么它的一致性是最终一致性的。其次是 CAP 原理中的一致性是指可线性化。核心原理是虽然整个系统是由多副本组成，但是通过线性化能力支持，对 client 而言就如一个副本，应用程序无需关心系统有多少个副本。然后是一致性哈希，它是一种分布式系统中的数据分片算法，具备良好的分散性、平衡性。最后是事务中的一致性，它是指事务变更前后，数据库必须满足若干恒等条件的状态约束，一致性往往是由数据库和业务程序两方面来保障的。


**在 Alice 向 Bob 转账的案例中有哪些恒等状态呢？**

很明显，转账系统内的各账号资金总额，在转账前后应该一致，同时各账号资产不能小于 0。为了帮助你更好地理解前面转账事务实现的问题，下面我给你画了幅两个并发转账事务的流程图。图中有两个并发的转账事务，Mike 向 Bob 转账 100 元，Alice 也向 Bob 转账 100 元，按照我们上面的事务实现，从下图可知转账前系统总资金是 600 元，转账后却只有 500 元了，因此它无法保证转账前后账号系统内的资产一致性，导致了资产凭空消失，破坏了事务的一致性。

![img](https://static001.geekbang.org/resource/image/1f/ea/1ff951756c0ffc427e5a064e3cf8caea.png?wh=1920*1153)

事务一致性被破坏的根本原因是，事务中缺少对 Bob 账号资产是否发生变化的判断，这就导致账号资金被覆盖。为了确保事务的一致性，一方面，业务程序在转账逻辑里面，需检查转账者资产大于等于转账金额。在事务提交时，通过账号资产的版本号，确保双方账号资产未被其他事务修改。若双方账号资产被其他事务修改，账号资产版本号会检查失败，这时业务可以通过获取最新的资产和版本号，发起新的转账事务流程解决。另一方面，etcd 会通过 WAL 日志和 consistent index、boltdb 事务特性，去确保事务的原子性，因此不会有部分成功部分失败的操作，导致资金凭空消失、新增。介绍完事务的原子性和持久化、一致性后，我们再看看 etcd 又是如何提供各种隔离级别的事务，在转账过程中，其他 client 能看到转账的中间状态吗 (如 Alice 扣款成功，Bob 还未增加时)？


### 隔离性
ACID 中的 I 是指 Isolation，也就是事务的隔离性，它是指事务在执行过程中的可见性。常见的事务隔离级别有以下四种。首先是未提交读（Read UnCommitted），也就是一个 client 能读取到未提交的事务。比如转账事务过程中，Alice 账号资金扣除后，Bob 账号上资金还未增加，这时如果其他 client 读取到这种中间状态，它会发现系统总金额钱减少了，破坏了事务一致性的约束。其次是已提交读（Read Committed），指的是只能读取到已经提交的事务数据，但是存在不可重复读的问题。比如事务开始时，你读取了 Alice 和 Bob 资金，这时其他事务修改 Alice 和 Bob 账号上的资金，你在事务中再次读取时会读取到最新资金，导致两次读取结果不一样。接着是可重复读（Repeated Read），它是指在一个事务中，同一个读操作 get Alice/Bob 在事务的任意时刻都能得到同样的结果，其他修改事务提交后也不会影响你本事务所看到的结果。最后是串行化（Serializable），它是最高的事务隔离级别，读写相互阻塞，通过牺牲并发能力、串行化来解决事务并发更新过程中的隔离问题。对于串行化我要和你特别补充一点，很多人认为它都是通过读写锁，来实现事务一个个串行提交的，其实这只是在基于锁的并发控制数据库系统实现而已。**为了优化性能，在基于 MVCC 机制实现的各个数据库系统中，提供了一个名为“可串行化的快照隔离”级别，相比悲观锁而言，它是一种乐观并发控制，通过快照技术实现的类似串行化的效果，事务提交时能检查是否冲突。**


下面我重点和你介绍下未提交读、已提交读、可重复读、串行化快照隔离。


### 未提交读
首先是最低的事务隔离级别，未提交读。我们通过如下一个转账事务时间序列图，来分析下一个 client 能否读取到未提交事务修改的数据，是否存在脏读。

![img](https://static001.geekbang.org/resource/image/6a/8d/6a526be4949a383fd5263484c706d68d.png?wh=1920*786)



图中有两个事务，一个是用户查询 Alice 和 Bob 资产的事务，一个是我们执行 Alice 向 Bob 转账的事务。如图中所示，若在 Alice 向 Bob 转账事务执行过程中，etcd server 收到了 client 查询 Alice 和 Bob 资产的读请求，显然此时我们无法接受 client 能读取到一个未提交的事务，因为这对应用程序而言会产生严重的 BUG。那么 etcd 是如何保证不出现这种场景呢？我们知道 etcd 基于 boltdb 实现读写操作的，读请求由 boltdb 的读事务处理，你可以理解为快照读。写请求由 boltdb 写事务处理，etcd 定时将一批写操作提交到 boltdb 并清空 buffer。由于 etcd 是批量提交写事务的，而读事务又是快照读，因此当 MVCC 写事务完成时，它需要更新 buffer，这样下一个读请求到达时，才能从 buffer 中获取到最新数据。在我们的场景中，转账事务并未结束，执行 put Alice 为 100 的操作不会回写 buffer，因此避免了脏读的可能性。用户此刻从 boltdb 快照读事务中查询到的 Alice 和 Bob 资产都为 200。从以上分析可知，etcd 并未使用悲观锁来解决脏读的问题，而是通过 MVCC 机制来实现读写不阻塞，并解决脏读的问题。



### 已提交读、可重复读
比未提交读隔离级别更高的是已提交读，它是指在事务中能读取到已提交数据，但是存在不可重复读的问题。已提交读，也就是说你每次读操作，若未增加任何版本号限制，默认都是当前读，etcd 会返回最新已提交的事务结果给你。如何理解不可重复读呢?在上面用户查询 Alice 和 Bob 事务的案例中，第一次查出来资产都是 200，第二次是 Alice 为 100，Bob 为 300，通过读已提交模式，你能及时获取到 etcd 最新已提交的事务结果，但是出现了不可重复读，两次读出来的 Alice 和 Bob 资产不一致。那么如何实现可重复读呢？你可以通过 MVCC 快照读，或者参考 etcd 的事务框架 STM 实现，它在事务中维护一个读缓存，优先从读缓存中查找，不存在则从 etcd 查询并更新到缓存中，这样事务中后续读请求都可从缓存中查找，确保了可重复读。最后我们再来重点介绍下什么是串行化快照隔离。



### 串行化快照隔离
串行化快照隔离是最严格的事务隔离级别，它是指在在事务刚开始时，首先获取 etcd 当前的版本号 rev，事务中后续发出的读请求都带上这个版本号 rev，告诉 etcd 你需要获取那个时间点的快照数据，etcd 的 MVCC 机制就能确保事务中能读取到同一时刻的数据。同时，它还要确保事务提交时，你读写的数据都是最新的，未被其他人修改，也就是要增加冲突检测机制。当事务提交出现冲突的时候依赖 client 重试解决，安全地实现多 key 原子更新。那么我们应该如何为上面一致性案例中，两个并发转账的事务，增加冲突检测机制呢？核心就是我们前面介绍 MVCC 的版本号，我通过下面的并发转账事务流程图为你解释它是如何工作的。

![img](https://static001.geekbang.org/resource/image/3b/26/3b4c7fb43e03a38aceb2a8c2d5c92226.png?wh=1920*1011)

如上图所示，事务 A，Alice 向 Bob 转账 100 元，事务 B，Mike 向 Bob 转账 100 元，两个事务同时发起转账操作。一开始时，Mike 的版本号 (指 mod_revision) 是 4，Bob 版本号是 3，Alice 版本号是 2，资产各自 200。为了防止并发写事务冲突，etcd 在一个写事务开始时，会独占一个 MVCC 读写锁。事务 A 会先去 etcd 查询当前 Alice 和 Bob 的资产版本号，用于在事务提交时做冲突检测。在事务 A 查询后，事务 B 获得 MVCC 写锁并完成转账事务，Mike 和 Bob 账号资产分别为 100，300，版本号都为 5。事务 B 完成后，事务 A 获得写锁，开始执行事务。为了解决并发事务冲突问题，事务 A 中增加了冲突检测，期望的 Alice 版本号应为 2，Bob 为 3。结果事务 B 的修改导致 Bob 版本号变成了 5，因此此事务会执行失败分支，再次查询 Alice 和 Bob 版本号和资产，发起新的转账事务，成功通过 MVCC 冲突检测规则 mod(“Alice”) = 2 和 mod(“Bob”) = 5 后，更新 Alice 账号资产为 100，Bob 资产为 400，完成转账操作。通过上面介绍的快照读和 MVCC 冲突检测检测机制，etcd 就可实现串行化快照隔离能力。

### 转账案例应用
介绍完 etcd 事务 ACID 特性实现后，你很容易发现事务特性初体验中的案例问题了，它缺少了完整事务的冲突检测机制。首先你可通过一个事务获取 Alice 和 Bob 账号的上资金和版本号，用以判断 Alice 是否有足够的金额转账给 Bob 和事务提交时做冲突检测。 你可通过如下 etcdctl txn 命令，获取 Alice 和 Bob 账号的资产和最后一次修改时的版本号 (mod_revision):


```
$ etcdctl txn -i -w=json
compares:


success requests (get, put, del):
get Alice
get Bob


failure requests (get, put, del):


{
 "kvs":[
      {
          "key":"QWxpY2U=",
          "create_revision":2,
          "mod_revision":2,
          "version":1,
          "value":"MjAw"
      }
  ],
    ......
  "kvs":[
      {
          "key":"Qm9i",
          "create_revision":3,
          "mod_revision":3,
          "version":1,
          "value":"MzAw"
      }
  ],
}
```

其次发起资金转账操作，Alice 账号减去 100，Bob 账号增加 100。为了保证转账事务的准确性、一致性，提交事务的时候需检查 Alice 和 Bob 账号最新修改版本号与读取资金时的一致 (compares 操作中增加版本号检测)，以保证其他事务未修改两个账号的资金。若 compares 操作通过检查，则执行转账操作，否则执行查询 Alice 和 Bob 账号资金操作，命令如下:

```
$ etcdctl txn -i
compares:
mod("Alice") = "2"
mod("Bob") = "3"


success requests (get, put, del):
put Alice 100
put Bob 300


failure requests (get, put, del):
get Alice
get Bob


SUCCESS


OK

OK
```
到这里我们就完成了一个安全的转账事务操作，从以上流程中你可以发现，自己从 0 到 1 实现一个完整的事务还是比较繁琐的，幸运的是，etcd 社区基于以上介绍的事务特性，提供了一个简单的事务框架[STM](https://github.com/etcd-io/etcd/blob/v3.4.9/clientv3/concurrency/stm.go)，构建了各个事务隔离级别类，帮助你进一步简化应用编程复杂度。


### 小结
最后我们来小结下今天的内容。首先我给你介绍了事务 API 的基本结构，它由 If、Then、Else 语句组成。其中 If 支持多个比较规则，它是用于事务提交时的冲突检测，比较的对象支持 key 的 mod_revision、create_revision、version、value 值。随后我给你介绍了整个事务执行的基本流程，Apply 模块首先执行 If 的比较规则，为真则执行 Then 语句，否则执行 Else 语句。接着通过转账案例，四幅转账事务时间序列图，我为你分析了事务的 ACID 特性，剖析了在 etcd 中事务的 ACID 特性的实现。

原子性是指一个事务要么全部成功要么全部失败，etcd 基于 WAL 日志、consistent index、boltdb 的事务能力提供支持。一致性是指事务转账前后的，数据库和应用程序期望的恒等状态应该保持不变，这通过数据库和业务应用程序相互协作完成。持久性是指事务提交后，数据不丢失，隔离性是指事务提交过程中的可见性，etcd 不存在脏读，基于 MVCC 机制、boltdb 事务你可以实现可重复读、串行化快照隔离级别的事务，保障并发事务场景中你的数据安全性。


## 10 | boltdb：如何持久化存储你的key-value数据？
在前面的课程里，我和你多次提到过 etcd 数据存储在 boltdb。那么 boltdb 是如何组织你的 key-value 数据的呢？当你读写一个 key 时，boltdb 是如何工作的？今天我将通过一个写请求在 boltdb 中执行的简要流程，分析其背后的 boltdb 的磁盘文件布局，帮助你了解 page、node、bucket 等核心数据结构的原理与作用，搞懂 boltdb 基于 B+ tree、各类 page 实现查找、更新、事务提交的原理，让你明白 etcd 为什么适合读多写少的场景。

### boltdb 磁盘布局
在介绍一个 put 写请求在 boltdb 中执行原理前，我先给你从整体上介绍下平时你所看到的 etcd db 文件的磁盘布局，让你了解下 db 文件的物理存储结构。boltdb 文件指的是你 etcd 数据目录下的 member/snap/db 的文件， etcd 的 key-value、lease、meta、member、cluster、auth 等所有数据存储在其中。etcd 启动的时候，会通过 mmap 机制将 db 文件映射到内存，后续可从内存中快速读取文件中的数据。写请求通过 fwrite 和 fdatasync 来写入、持久化数据到磁盘。

![img](https://static001.geekbang.org/resource/image/a6/41/a6086a069a2cf52b38d60716780f2e41.png?wh=1920*1131)

上图是我给你画的 db 文件磁盘布局，从图中的左边部分你可以看到，文件的内容由若干个 page 组成，一般情况下 page size 为 4KB。page 按照功能可分为元数据页 (meta page)、B+ tree 索引节点页 (branch page)、B+ tree 叶子节点页 (leaf page)、空闲页管理页 (freelist page)、空闲页 (free page)。文件最开头的两个 page 是固定的 db 元数据 meta page，空闲页管理页记录了 db 中哪些页是空闲、可使用的。索引节点页保存了 B+ tree 的内部节点，如图中的右边部分所示，它们记录了 key 值，叶子节点页记录了 B+ tree 中的 key-value 和 bucket 数据。boltdb 逻辑上通过 B+ tree 来管理 branch/leaf page， 实现快速查找、写入 key-value 数据。

### boltdb API
了解完 boltdb 的磁盘布局后，那么如果要在 etcd 中执行一个 put 请求，boltdb 中是如何执行的呢？ boltdb 作为一个库，提供了什么 API 给 client 访问写入数据？boltdb 提供了非常简单的 API 给上层业务使用，当我们执行一个 put hello 为 world 命令时，boltdb 实际写入的 key 是版本号，value 为 mvccpb.KeyValue 结构体。这里我们简化下，假设往 key bucket 写入一个 key 为 r94，value 为 world 的字符串，其核心代码如下：

```
// 打开boltdb文件，获取db对象
db,err := bolt.Open("db"， 0600， nil)
if err != nil {
   log.Fatal(err)
}
defer db.Close()
// 参数true表示创建一个写事务，false读事务
tx,err := db.Begin(true)
if err != nil {
   return err
}
defer tx.Rollback()
// 使用事务对象创建key bucket
b,err := tx.CreatebucketIfNotExists([]byte("key"))
if err != nil {
   return err
}
// 使用bucket对象更新一个key
if err := b.Put([]byte("r94"),[]byte("world")); err != nil {
   return err
}
// 提交事务
if err := tx.Commit(); err != nil {
   return err
}
```
如上所示，通过 boltdb 的 Open API，我们获取到 boltdb 的核心对象 db 实例后，然后通过 db 的 Begin API 开启写事务，获得写事务对象 tx。通过写事务对象 tx， 你可以创建 bucket。这里我们创建了一个名为 key 的 bucket（如果不存在），并使用 bucket API 往其中更新了一个 key 为 r94，value 为 world 的数据。最后我们使用写事务的 Commit 接口提交整个事务，完成 bucket 创建和 key-value 数据写入。看起来是不是非常简单，神秘的 boltdb，并未有我们想象的那么难。然而其 API 简单的背后却是 boltdb 的一系列巧妙的设计和实现。一个 key-value 数据如何知道该存储在 db 在哪个 page？如何快速找到你的 key-value 数据？事务提交的原理又是怎样的呢？接下来我就和你浅析 boltdb 背后的奥秘。



### 核心数据结构介绍
上面我们介绍 boltdb 的磁盘布局时提到，boltdb 整个文件由一个个 page 组成。最开头的两个 page 描述 db 元数据信息，而它正是在 client 调用 boltdb Open API 时被填充的。那么描述磁盘页面的 page 数据结构是怎样的呢？元数据页又含有哪些核心数据结构？boltdb 本身自带了一个工具 bbolt，它可以按页打印出 db 文件的十六进制的内容，下面我们就使用此工具来揭开 db 文件的神秘面纱。下图左边的十六进制是执行如下[bbolt dump](https://github.com/etcd-io/bbolt/blob/master/cmd/bbolt/main.go)命令，所打印的 boltdb 第 0 页的数据，图的右边是对应的 page 磁盘页结构和 meta page 的数据结构。

```
$ ./bbolt dump ./infra1.etcd/member/snap/db 0
```

![img](https://static001.geekbang.org/resource/image/94/16/94a4b5yydab7yy9a3f340632274f9616.png?wh=1920*1242)

一看上图中的十六进制数据，你可能很懵，没关系，在你了解 page 磁盘页结构、meta page 数据结构后，你就能读懂其含义了。


### page 磁盘页结构
我们先了解下 page 磁盘页结构，如上图所示，它由页 ID(id)、页类型 (flags)、数量 (count)、溢出页数量 (overflow)、页面数据起始位置 (ptr) 字段组成。页类型目前有如下四种：0x01 表示 branch page，0x02 表示 leaf page，0x04 表示 meta page，0x10 表示 freelist page。数量字段仅在页类型为 leaf 和 branch 时生效，溢出页数量是指当前页面数据存放不下，需要向后再申请 overflow 个连续页面使用，页面数据起始位置指向 page 的载体数据，比如 meta page、branch/leaf 等 page 的内容。


### meta page 数据结构
第 0、1 页我们知道它是固定存储 db 元数据的页 (meta page)，那么 meta page 它为了管理整个 boltdb 含有哪些信息呢？如上图中的 meta page 数据结构所示，你可以看到它由 boltdb 的文件标识 (magic)、版本号 (version)、页大小 (pagesize)、boltdb 的根 bucket 信息 (root bucket)、freelist 页面 ID(freelist)、总的页面数量 (pgid)、上一次写事务 ID(txid)、校验码 (checksum) 组成。

### meta page 十六进制分析
了解完 page 磁盘页结构和 meta page 数据结构后，我再结合图左边的十六进数据和你简要分析下其含义。上图中十六进制输出的是 db 文件的 page 0 页结构，左边第一列表示此行十六进制内容对应的文件起始地址，每行 16 个字节。结合 page 磁盘页和 meta page 数据结构我们可知，第一行前 8 个字节描述 pgid(忽略第一列) 是 0。接下来 2 个字节描述的页类型， 其值为 0x04 表示 meta page， 说明此页的数据存储的是 meta page 内容，因此 ptr 开始的数据存储的是 meta page 内容。正如你下图中所看到的，第二行首先含有一个 4 字节的 magic number(0xED0CDAED)，通过它来识别当前文件是否 boltdb，接下来是两个字节描述 boltdb 的版本号 0x2， 然后是四个字节的 page size 大小，0x1000 表示 4096 个字节，四个字节的 flags 为 0。

![img](https://static001.geekbang.org/resource/image/09/c0/09d8a9174b4539718878fcfb9da84cc0.png?wh=411*161)



第三行对应的就是 meta page 的 root bucket 结构（16 个字节），它描述了 boltdb 的 root bucket 信息，比如一个 db 中有哪些 bucket， bucket 里面的数据存储在哪里。第四行中前面的 8 个字节，0x3 表示 freelist 页面 ID，此页面记录了 db 当前哪些页面是空闲的。后面 8 个字节，0x6 表示当前 db 总的页面数。第五行前面的 8 个字节，0x1a 表示上一次的写事务 ID，后面的 8 个字节表示校验码，用于检测文件是否损坏。了解完 db 元数据页面原理后，那么 boltdb 是如何根据元数据页面信息快速找到你的 bucket 和 key-value 数据呢？这就涉及到了元数据页面中的 root bucket，它是个至关重要的数据结构。下面我们看看它是如何管理一系列 bucket、帮助我们查找、写入 key-value 数据到 boltdb 中。


### bucket 数据结构
如下命令所示，你可以使用 bbolt buckets 命令，输出一个 db 文件的 bucket 列表。执行完此命令后，我们可以看到之前介绍过的 auth/lease/meta 等熟悉的 bucket，它们都是 etcd 默认创建的。那么 boltdb 是如何存储、管理 bucket 的呢？


```
$ ./bbolt buckets  ./infra1.etcd/member/snap/db
alarm
auth
authRoles
authUsers
cluster
key
lease
members
members_removed
meta
```

在上面我们提到过 meta page 中的，有一个名为 root、类型 bucket 的重要数据结构，如下所示，bucket 由 root 和 sequence 两个字段组成，root 表示该 bucket 根节点的 page id。注意 meta page 中的 bucket.root 字段，存储的是 db 的 root bucket 页面信息，你所看到的 key/lease/auth 等 bucket 都是 root bucket 的子 bucket。


```
type bucket struct {
   root     pgid   // page id of the bucket's root-level page
   sequence uint64 // monotonically incrementing, used by NextSequence()
}
```

![img](https://static001.geekbang.org/resource/image/14/9b/14f9c6f5061f44ea3c1d8de4f47a5b9b.png?wh=1920*719)

上面 meta page 十六进制图中，第三行的 16 个字节就是描述的 root bucket 信息。root bucket 指向的 page id 为 4，page id 为 4 的页面是什么类型呢？ 我们可以通过如下 bbolt pages 命令看看各个 page 类型和元素数量，从下图结果可知，4 号页面为 leaf page。


```
$ ./bbolt pages  ./infra1.etcd/member/snap/db
ID       TYPE       ITEMS  OVRFLW
======== ========== ====== ======
0        meta       0
1        meta       0
2        free
3        freelist   2
4        leaf       10
5        free
```

通过上面的分析可知，当 bucket 比较少时，我们子 bucket 数据可直接从 meta page 里指向的 leaf page 中找到。


### leaf page
meta page 的 root bucket 直接指向的是 page id 为 4 的 leaf page， page flag 为 0x02， leaf page 它的磁盘布局如下图所示，前半部分是 leafPageElement 数组，后半部分是 key-value 数组。

![img](https://static001.geekbang.org/resource/image/0e/e8/0e70f52dc9752e2yy19f74a044530ee8.png?wh=1920*1013)

leafPageElement 包含 leaf page 的类型 flags， 通过它可以区分存储的是 bucket 名称还是 key-value 数据。当 flag 为 bucketLeafFlag(0x01) 时，表示存储的是 bucket 数据，否则存储的是 key-value 数据，leafPageElement 它还含有 key-value 的读取偏移量，key-value 大小，根据偏移量和 key-value 大小，我们就可以方便地从 leaf page 中解析出所有 key-value 对。当存储的是 bucket 数据的时候，key 是 bucket 名称，value 则是 bucket 结构信息。bucket 结构信息含有 root page 信息，通过 root page（基于 B+ tree 查找算法），你可以快速找到你存储在这个 bucket 下面的 key-value 数据所在页面。从上面分析你可以看到，每个子 bucket 至少需要一个 page 来存储其下面的 key-value 数据，如果子 bucket 数据量很少，就会造成磁盘空间的浪费。实际上 boltdb 实现了 inline bucket，在满足一些条件限制的情况下，可以将小的子 bucket 内嵌在它的父亲叶子节点上，友好的支持了大量小 bucket。为了方便大家快速理解核心原理，本节我们讨论的 bucket 是假设都是非 inline bucket。那么 boltdb 是如何管理大量 bucket、key-value 的呢？

### branch page
boltdb 使用了 B+ tree 来高效管理所有子 bucket 和 key-value 数据，因此它可以支持大量的 bucket 和 key-value，只不过 B+ tree 的根节点不再直接指向 leaf page，而是 branch page 索引节点页。branch page flags 为 0x01。它的磁盘布局如下图所示，前半部分是 branchPageElement 数组，后半部分是 key 数组。

![img](https://static001.geekbang.org/resource/image/61/9d/61af0c7e7e5beb05be6130bda29da49d.png?wh=1920*951)

branchPageElement 包含 key 的读取偏移量、key 大小、子节点的 page id。根据偏移量和 key 大小，我们就可以方便地从 branch page 中解析出所有 key，然后二分搜索匹配 key，获取其子节点 page id，递归搜索，直至从 bucketLeafFlag 类型的 leaf page 中找到目的 bucket name。注意，boltdb 在内存中使用了一个名为 node 的数据结构，来保存 page 反序列化的结果。下面我给出了一个 boltdb 读取 page 到 node 的代码片段，你可以直观感受下。

```go
func (n *node) read(p *page) {
   n.pgid = p.id
   n.isLeaf = ((p.flags & leafPageFlag) != 0)
   n.inodes = make(inodes, int(p.count))


   for i := 0; i < int(p.count); i++ {
      inode := &n.inodes[i]
      if n.isLeaf {
         elem := p.leafPageElement(uint16(i))
         inode.flags = elem.flags
         inode.key = elem.key()
         inode.value = elem.value()
      } else {
         elem := p.branchPageElement(uint16(i))
         inode.pgid = elem.pgid
         inode.key = elem.key()
      }
   }
```


从上面分析过程中你会发现，boltdb 存储 bucket 和 key-value 原理是类似的，将 page 划分成 branch page、leaf page，通过 B+ tree 来管理实现。boltdb 为了区分 leaf page 存储的数据类型是 bucket 还是 key-value，增加了标识字段（leafPageElement.flags），因此 key-value 的数据存储过程我就不再重复分析了。


### freelist
介绍完 bucket、key-value 存储原理后，我们再看 meta page 中的另外一个核心字段 freelist，它的作用是什么呢？我们知道 boltdb 将 db 划分成若干个 page，那么它是如何知道哪些 page 在使用中，哪些 page 未使用呢？答案是 boltdb 通过 meta page 中的 freelist 来管理页面的分配，freelist page 中记录了哪些页是空闲的。当你在 boltdb 中删除大量数据的时候，其对应的 page 就会被释放，页 ID 存储到 freelist 所指向的空闲页中。当你写入数据的时候，就可直接从空闲页中申请页面使用。下面 meta page 十六进制图中，第四行的前 8 个字节就是描述的 freelist 信息，page id 为 3。我们可以通过 bbolt page 命令查看 3 号 page 内容，如下所示，它记录了 2 和 5 为空闲页，与我们上面通过 bbolt pages 命令所看到的信息一致。

![img](https://static001.geekbang.org/resource/image/4a/9a/4a4d05678cfb785618537d2f930e859a.png?wh=1910*708)


```
$ ./bbolt page  ./infra1.etcd/member/snap/db 3
page ID:    3
page Type:  freelist
Total Size: 4096 bytes
Item Count: 2
Overflow: 0

2
5
```

下图是 freelist page 存储结构，pageflags 为 0x10，表示 freelist 类型的页，ptr 指向空闲页 id 数组。注意在 boltdb 中支持通过多种数据结构（数组和 hashmap）来管理 free page，这里我介绍的是数组。

![img](https://static001.geekbang.org/resource/image/57/bb/57c6dd899c4cb56198a6092855161ebb.png?wh=1920*1070)


### Open 原理
了解完核心数据结构后，我们就很容易搞懂 boltdb Open API 的原理了。首先它会打开 db 文件并对其增加文件锁，目的是防止其他进程也以读写模式打开它后，操作 meta 和 free page，导致 db 文件损坏。其次 boltdb 通过 mmap 机制将 db 文件映射到内存中，并读取两个 meta page 到 db 对象实例中，然后校验 meta page 的 magic、version、checksum 是否有效，若两个 meta page 都无效，那么 db 文件就出现了严重损坏，导致异常退出。



### Put 原理
那么成功获取 db 对象实例后，通过 bucket API 创建一个 bucket、发起一个 Put 请求更新数据时，boltdb 是如何工作的呢？根据我们上面介绍的 bucket 的核心原理，它首先是根据 meta page 中记录 root bucket 的 root page，按照 B+ tree 的查找算法，从 root page 递归搜索到对应的叶子节点 page 面，返回 key 名称、leaf 类型。如果 leaf 类型为 bucketLeafFlag，且 key 相等，那么说明已经创建过，不允许 bucket 重复创建，结束请求。否则往 B+ tree 中添加一个 flag 为 bucketLeafFlag 的 key，key 名称为 bucket name，value 为 bucket 的结构。创建完 bucket 后，你就可以通过 bucket 的 Put API 发起一个 Put 请求更新数据。它的核心原理跟 bucket 类似，根据子 bucket 的 root page，从 root page 递归搜索此 key 到 leaf page，如果没有找到，则在返回的位置处插入新 key 和 value。为了方便你理解 B+ tree 查找、插入一个 key 原理，我给你构造了的一个 max degree 为 5 的 B+ tree，下图是 key r94 的查找流程图。

那么如何确定这个 key 的插入位置呢？首先从 boltdb 的 key bucket 的 root page 里，二分查找大于等于 r94 的 key 所在 page，最终找到 key r9 指向的 page（流程 1）。r9 指向的 page 是个 leaf page，B+ tree 需要确保叶子节点 key 的有序性，因此同样二分查找其插入位置，将 key r94 插入到相关位置（流程二）。

![img](https://static001.geekbang.org/resource/image/e6/6e/e6d2c12de362b55c7c36c45e5b65706e.png?wh=1920*711)

在核心数据结构介绍中，我和你提到 boltdb 在内存中通过 node 数据结构来存储 page 磁盘页内容，它记录了 key-value 数据、page id、parent 及 children 的 node、B+ tree 是否需要进行重平衡和分裂操作等信息。因此，当我们执行完一个 put 请求时，它只是将值更新到 boltdb 的内存 node 数据结构里，并未持久化到磁盘中。

### 事务提交原理
那么 boltdb 何时将数据持久化到 db 文件中呢？当你的代码执行 tx.Commit API 时，它才会将我们上面保存到 node 内存数据结构中的数据，持久化到 boltdb 中。下图我给出了一个事务提交的流程图，接下来我就分别和你简要分析下各个核心步骤。

![img](https://static001.geekbang.org/resource/image/e9/6f/e93935835e792363ae2edc5290f2266f.png?wh=1536*1532)

首先从上面 put 案例中我们可以看到，插入了一个新的元素在 B+ tree 的叶子节点，它可能已不满足 B+ tree 的特性，因此事务提交时，第一步首先要调整 B+ tree，进行重平衡、分裂操作，使其满足 B+ tree 树的特性。上面案例里插入一个 key r94 后，经过重平衡、分裂操作后的 B+ tree 如下图所示。

![img](https://static001.geekbang.org/resource/image/d3/8c/d31f483a10abeff34a8fef37941ef28c.png?wh=1920*838)

在重平衡、分裂过程中可能会申请、释放 free page，freelist 所管理的 free page 也发生了变化。因此事务提交的第二步，就是持久化 freelist。注意，在 etcd v3.4.9 中，为了优化写性能等，freelist 持久化功能是关闭的。etcd 启动获取 boltdb db 对象的时候，boltdb 会遍历所有 page，构建空闲页列表。事务提交的第三步就是将 client 更新操作产生的 dirty page 通过 fdatasync 系统调用，持久化存储到磁盘中。最后，在执行写事务过程中，meta page 的 txid、freelist 等字段会发生变化，因此事务的最后一步就是持久化 meta page。通过以上四大步骤，我们就完成了事务提交的工作，成功将数据持久化到了磁盘文件中，安全地完成了一个 put 操作。

### 小结
最后我们来小结下今天的内容。首先我通过一幅 boltdb 磁盘布局图和 bbolt 工具，为你解密了 db 文件的本质。db 文件由 meta page、freelist page、branch page、leaf page、free page 组成。随后我结合 bbolt 工具，和你深入介绍了 meta page、branch page、leaf page、freelist page 的数据结构，帮助你了解 key、value 数据是如何存储到文件中的。然后我通过分析一个 put 请求在 boltdb 中如何执行的。我从 Open API 获取 db 对象说起，介绍了其通过 mmap 将 db 文件映射到内存，构建 meta page，校验 meta page 的有效性，再到创建 bucket，通过 bucket API 往 boltdb 添加 key-value 数据。添加 bucket 和 key-value 操作本质，是从 B+ tree 管理的 page 中找到插入的页和位置，并将数据更新到 page 的内存 node 数据结构中。真正持久化数据到磁盘是通过事务提交执行的。它首先需要通过一系列重平衡、分裂操作，确保 boltdb 维护的 B+ tree 满足相关特性，其次需要持久化 freelist page，并将用户更新操作产生的 dirty page 数据持久化到磁盘中，最后则是持久化 meta page。

## 11 | 压缩：如何回收旧版本数据？

这节课是我们基础篇里的最后一节，正巧这节课的内容也是最轻松的。新年新气象，我们就带着轻松的心情开始吧！在07里，我们知道 etcd 中的每一次更新、删除 key 操作，treeIndex 的 keyIndex 索引中都会追加一个版本号，在 boltdb 中会生成一个新版本 boltdb key 和 value。也就是随着你不停更新、删除，你的 etcd 进程内存占用和 db 文件就会越来越大。很显然，这会导致 etcd OOM 和 db 大小增长到最大 db 配额，最终不可写。那么 etcd 是通过什么机制来回收历史版本数据，控制索引内存占用和 db 大小的呢？这就是我今天要和你分享的 etcd 压缩机制。希望通过今天的这节课，能帮助你理解 etcd 压缩原理，在使用 etcd 过程中能根据自己的业务场景，选择适合的压缩策略，避免 db 大小增长失控而不可写入，帮助你构建稳定的 etcd 服务。

整体架构

![img](https://static001.geekbang.org/resource/image/7c/21/7c5d5212fa14yy6aaf843ae3dfc5f721.png?wh=1920*918)

在了解 etcd 压缩模块实现细节前，我先给你画了一幅压缩模块的整体架构图。从图中可知，你可以通过 client API 发起人工的压缩 (Compact) 操作，也可以配置自动压缩策略。在自动压缩策略中，你可以根据你的业务场景选择合适的压缩模式。目前 etcd 支持两种压缩模式，分别是时间周期性压缩和版本号压缩。当你通过 API 发起一个 Compact 请求后，KV Server 收到 Compact 请求提交到 Raft 模块处理，在 Raft 模块中提交后，Apply 模块就会通过 MVCC 模块的 Compact 接口执行此压缩任务。Compact 接口首先会更新当前 server 已压缩的版本号，并将耗时昂贵的压缩任务保存到 FIFO 队列中异步执行。压缩任务执行时，它首先会压缩 treeIndex 模块中的 keyIndex 索引，其次会遍历 boltdb 中的 key，删除已废弃的 key。以上就是压缩模块的一个工作流程。接下来我会首先和你介绍如何人工发起一个 Compact 操作，然后详细介绍周期性压缩模式、版本号压缩模式的工作原理，最后再给你介绍 Compact 操作核心的原理。


### 压缩特性初体验
在使用 etcd 过程中，当你遇到"etcdserver: mvcc: database space exceeded"错误时，若是你未开启压缩策略导致 db 大小达到配额，这时你可以使用 etcdctl compact 命令，主动触发压缩操作，回收历史版本。如下所示，你可以先通过 endpoint status 命令获取 etcd 当前版本号，然后再通过 etcdctl compact 命令发起压缩操作即可。
```
# 获取etcd当前版本号
$ rev=$(etcdctl endpoint status --write-out="json" | egrep -o '"revision":[0-9]*' | egrep -o '[0-9].*')
$ echo $rev
9
# 执行压缩操作，指定压缩的版本号为当前版本号
$ etcdctl compact $rev
Compacted revision 9
# 压缩一个已经压缩的版本号
$ etcdctl compact $rev
Error: etcdserver: mvcc: required revision has been compacted
# 压缩一个比当前最大版号大的版本号
$ etcdctl compact 12
Error: etcdserver: mvcc: required revision is a future revision
```

请注意，如果你压缩命令传递的版本号小于等于当前 etcd server 记录的压缩版本号，etcd server 会返回已压缩错误 ("mvcc: required revision has been compacted") 给 client。如果版本号大于当前 etcd server 最新的版本号，etcd server 则返回一个未来的版本号错误给 client("mvcc: required revision is a future revision")。执行压缩命令的时候，不少初学者有一个常见的误区，就是担心压缩会不会把我最新版本数据给删除？

压缩的本质是回收历史版本，目标对象仅是历史版本，不包括一个 key-value 数据的最新版本，因此你可以放心执行压缩命令，不会删除你的最新版本数据。不过我在08介绍 Watch 机制时提到，Watch 特性中的历史版本数据同步，依赖于 MVCC 中是否还保存了相关数据，因此我建议你不要每次简单粗暴地回收所有历史版本。在生产环境中，我建议你精细化的控制历史版本数，那如何实现精细化控制呢？主要有两种方案，一种是使用 etcd server 的自带的自动压缩机制，根据你的业务场景，配置合适的压缩策略即可。另外一种方案是如果你觉得 etcd server 的自带压缩机制无法满足你的诉求，想更精细化的控制 etcd 保留的历史版本记录，你就可以基于 etcd 的 Compact API，在业务逻辑代码中、或定时任务中主动触发压缩操作。你需要确保发起 Compact 操作的程序高可用，压缩的频率、保留的历史版本在合理范围内，并最终能使 etcd 的 db 大小保持平稳，否则会导致 db 大小不断增长，直至 db 配额满，无法写入。在一般情况下，我建议使用 etcd 自带的压缩机制。它支持两种模式，分别是按时间周期性压缩和保留版本号的压缩，配置相应策略后，etcd 节点会自动化的发起 Compact 操作。接下来我就和你详细介绍下 etcd 的周期性和保留版本号压缩模式。




### 周期性压缩
首先是周期性压缩模式，它适用于什么场景呢？当你希望 etcd 只保留最近一段时间写入的历史版本时，你就可以选择配置 etcd 的压缩模式为 periodic，保留时间为你自定义的 1h 等。如何给 etcd server 配置压缩模式和保留时间呢?如下所示，etcd server 提供了配置压缩模式和保留时间的参数：


```
--auto-compaction-retention '0'
Auto compaction retention length. 0 means disable auto Compaction.
--auto-compaction-mode 'periodic'
Interpret 'auto-Compaction-retention' one of: periodic|revision.
```

auto-compaction-mode 为 periodic 时，它表示启用时间周期性压缩，auto-compaction-retention 为保留的时间的周期，比如 1h。auto-compaction-mode 为 revision 时，它表示启用版本号压缩模式，auto-compaction-retention 为保留的历史版本号数，比如 10000。注意，etcd server 的 auto-compaction-retention 为'0'时，将关闭自动压缩策略，那么周期性压缩模式的原理是怎样的呢？ etcd 是如何知道你配置的 1h 前的 etcd server 版本号呢？其实非常简单，etcd server 启动后，根据你的配置的模式 periodic，会创建 periodic Compactor，它会异步的获取、记录过去一段时间的版本号。periodic Compactor 组件获取你设置的压缩间隔参数 1h， 并将其划分成 10 个区间，也就是每个区间 6 分钟。每隔 6 分钟，它会通过 etcd MVCC 模块的接口获取当前的 server 版本号，追加到 rev 数组中。因为你只需要保留过去 1 个小时的历史版本，periodic Compactor 组件会通过当前时间减去上一次成功执行 Compact 操作的时间，如果间隔大于一个小时，它会取出 rev 数组的首元素，通过 etcd server 的 Compact 接口，发起压缩操作。需要注意的一点是，在 etcd v3.3.3 版本之前，不同的 etcd 版本对周期性压缩的行为是有一定差异的，具体的区别你可以参考下[官方文档](https://github.com/etcd-io/etcd/blob/v3.4.9/Documentation/op-guide/maintenance.md)。

### 版本号压缩
了解完周期性压缩模式，我们再看看版本号压缩模式，它又适用于什么场景呢？当你写请求比较多，可能产生比较多的历史版本导致 db 增长时，或者不确定配置 periodic 周期为多少才是最佳的时候，你可以通过设置压缩模式为 revision，指定保留的历史版本号数。比如你希望 etcd 尽量只保存 1 万个历史版本，那么你可以指定 compaction-mode 为 revision，auto-compaction-retention 为 10000。它的实现原理又是怎样的呢?

也很简单，etcd 启动后会根据你的压缩模式 revision，创建 revision Compactor。revision Compactor 会根据你设置的保留版本号数，每隔 5 分钟定时获取当前 server 的最大版本号，减去你想保留的历史版本数，然后通过 etcd server 的 Compact 接口发起如下的压缩操作即可。


```
# 获取当前版本号，减去保留的版本号数
rev := rc.rg.Rev() - rc.retention
# 调用server的Compact接口压缩
_，err := rc.c.Compact(rc.ctx，&pb.CompactionRequest{Revision: rev})
```

### 压缩原理
介绍完两种自动化的压缩模式原理后，接下来我们就深入分析下压缩的本质。当 etcd server 收到 Compact 请求后，它是如何执行的呢？ 核心原理是什么？如前面的整体架构图所述，Compact 请求经过 Raft 日志同步给多数节点后，etcd 会从 Raft 日志取出 Compact 请求，应用此请求到状态机执行。执行流程如下图所示，MVCC 模块的 Compact 接口首先会检查 Compact 请求的版本号 rev 是否已被压缩过，若是则返回 ErrCompacted 错误给 client。其次会检查 rev 是否大于当前 etcd server 的最大版本号，若是则返回 ErrFutureRev 给 client，这就是我们上面执行 etcdctl compact 命令所看到的那两个错误原理。通过检查后，Compact 接口会通过 boltdb 的 API 在 meta bucket 中更新当前已调度的压缩版本号 (scheduledCompactedRev) 号，然后将压缩任务追加到 FIFO Scheduled 中，异步调度执行。

![img](https://static001.geekbang.org/resource/image/9a/ff/9ac55d639f564b56324b96dc02f0c0ff.png?wh=1920*1332)

为什么 Compact 接口需要持久化存储当前已调度的压缩版本号到 boltdb 中呢？试想下如果不保存这个版本号，etcd 在异步执行的 Compact 任务过程中 crash 了，那么异常节点重启后，各个节点数据就会不一致。因此 etcd 通过持久化存储 scheduledCompactedRev，节点 crash 重启后，会重新向 FIFO Scheduled 中添加压缩任务，已保证各个节点间的数据一致性。异步的执行压缩任务会做哪些工作呢？

首先我们回顾下07里介绍的 treeIndex 索引模块，它是 etcd 支持保存历史版本的核心模块，每个 key 在 treeIndex 模块中都有一个 keyIndex 数据结构，记录其历史版本号信息。

![img](https://static001.geekbang.org/resource/image/4f/dc/4f9cb015a842da0d5bd556d6b45970dc.png?wh=1920*1124)



如上图所示，因此异步压缩任务的第一项工作，就是**压缩 treeIndex 模块中的各 key 的历史版本、已删除的版本**。为了避免压缩工作影响读写性能，首先会克隆一个 B-tree，然后通过克隆后的 B-tree 遍历每一个 keyIndex 对象，压缩历史版本号、清理已删除的版本。假设当前压缩的版本号是 CompactedRev， 它会保留 keyIndex 中最大的版本号，移除小于等于 CompactedRev 的版本号，并通过一个 map 记录 treeIndex 中有效的版本号返回给 boltdb 模块使用。为什么要保留最大版本号呢?

因为最大版本号是这个 key 的最新版本，移除了会导致 key 丢失。而 Compact 的目的是回收旧版本。当然如果 keyIndex 中的最大版本号被打了删除标记 (tombstone)， 就会从 treeIndex 中删除这个 keyIndex，否则会出现内存泄露。Compact 任务执行完索引压缩后，它通过遍历 B-tree、keyIndex 中的所有 generation 获得当前内存索引模块中有效的版本号，这些信息将帮助 etcd 清理 boltdb 中的废弃历史版本。

![img](https://static001.geekbang.org/resource/image/d6/70/d625753e5a7f0f7f37987764b9204270.png?wh=1920*1129)



压缩任务的第二项工作就是**删除 boltdb 中废弃的历史版本数据**。如上图所示，它通过 etcd 一个名为 scheduleCompaction 任务来完成。scheduleCompaction 任务会根据 key 区间，从 0 到 CompactedRev 遍历 boltdb 中的所有 key，通过 treeIndex 模块返回的有效索引信息，判断这个 key 是否有效，无效则调用 boltdb 的 delete 接口将 key-value 数据删除。在这过程中，scheduleCompaction 任务还会更新当前 etcd 已经完成的压缩版本号 (finishedCompactRev)，将其保存到 boltdb 的 meta bucket 中。scheduleCompaction 任务遍历、删除 key 的过程可能会对 boltdb 造成压力，为了不影响正常读写请求，它在执行过程中会通过参数控制每次遍历、删除的 key 数（默认为 100，每批间隔 10ms），分批完成 boltdb key 的删除操作。



### 为什么压缩后 db 大小不减少呢?
当你执行完压缩任务后，db 大小减少了吗？ 事实是并没有减少。那为什么我们都通过 boltdb API 删除了 key，db 大小还不减少呢？上节课我们介绍 boltdb 实现时，提到过 boltdb 将 db 文件划分成若干个 page 页，page 页又有四种类型，分别是 meta page、branch page、leaf page 以及 freelist page。branch page 保存 B+ tree 的非叶子节点 key 数据，leaf page 保存 bucket 和 key-value 数据，freelist 会记录哪些页是空闲的。

当我们通过 boltdb 删除大量的 key，在事务提交后 B+ tree 经过分裂、平衡，会释放出若干 branch/leaf page 页面，然而 boltdb 并不会将其释放给磁盘，调整 db 大小操作是昂贵的，会对性能有较大的损害。boltdb 是通过 freelist page 记录这些空闲页的分布位置，当收到新的写请求时，优先从空闲页数组中申请若干连续页使用，实现高性能的读写（而不是直接扩大 db 大小）。当连续空闲页申请无法得到满足的时候，  boltdb 才会通过增大 db 大小来补充空闲页。一般情况下，压缩操作释放的空闲页就能满足后续新增写请求的空闲页需求，db 大小会趋于整体稳定。


### 小结
最后我们来小结下今天的内容。etcd 压缩操作可通过 API 人工触发，也可以配置压缩模式由 etcd server 自动触发。压缩模式支持按周期和版本两种。在周期模式中你可以实现保留最近一段时间的历史版本数，在版本模式中你可以实现保留期望的历史版本数。压缩的核心工作原理分为两大任务，第一个任务是压缩 treeIndex 中的各 key 历史索引，清理已删除 key，并将有效的版本号保存到 map 数据结构中。第二个任务是删除 boltdb 中的无效 key。基本原理是根据版本号遍历 boltdb 已压缩区间范围的 key，通过 treeIndex 返回的有效索引 map 数据结构判断 key 是否有效，无效则通过 boltdb API 删除它。最后在执行压缩的操作中，虽然我们删除了 boltdb db 的 key-value 数据，但是 db 大小并不会减少。db 大小不变的原因是存放 key-value 数据的 branch 和 leaf 页，它们释放后变成了空闲页，并不会将空间释放给磁盘。boltdb 通过 freelist page 来管理一系列空闲页，后续新增的写请求优先从 freelist 中申请空闲页使用，以提高性能。在写请求速率稳定、新增 key-value 较少的情况下，压缩操作释放的空闲页就可以基本满足后续写请求对空闲页的需求，db 大小就会处于一个基本稳定、健康的状态。




## 实践篇

## 12 | 一致性：为什么基于Raft实现的etcd还会出现数据不一致？

今天我要和你分享的主题是关于 etcd 数据一致性的。我们都知道 etcd 是基于 Raft 实现的高可用、强一致分布式存储。但是有一天我和小伙伴王超凡却遭遇了一系列诡异的现象：用户在更新 Kubernetes 集群中的 Deployment 资源镜像后，无法创建出新 Pod，Deployment 控制器莫名其妙不工作了。更令人细思极恐的是，部分 Node 莫名其妙消失了。我们当时随便找了一个 etcd 节点查看存储数据，发现 Node 节点却在。这究竟是怎么一回事呢？ 今天我将和你分享这背后的故事，以及由它带给我们的教训和启发。希望通过这节课，能帮助你搞懂为什么基于 Raft 实现的 etcd 有可能出现数据不一致，以及我们应该如何提前规避、预防类似问题。


### 从消失的 Node 说起
故事要从去年 1 月的时候说起，某日晚上我们收到一个求助，有人反馈 Kubernetes 集群出现了 Deployment 滚动更新异常、节点莫名其妙消失了等诡异现象。我一听就感觉里面可能大有文章，于是开始定位之旅。我首先查看了下 Kubernetes 集群 APIServer、Controller Manager、Scheduler 等组件状态，发现都是正常。然后我查看了下 etcd 集群各节点状态，也都是健康的，看了一个 etcd 节点数据也是正常，于是我开始怀疑是不是 APIServer 出现了什么诡异的 Bug 了。我尝试重启 APIServer，可 Node 依旧消失。百思不得其解的同时，只能去确认各个 etcd 节点上数据是否存在，结果却有了颠覆你固定思维的发现，那就是基于 Raft 实现的强一致存储竟然出现不一致、数据丢失。除了第一个节点含有数据，另外两个节点竟然找不到。那么问题就来了，另外两个节点数据是如何丢失的呢？

### 一步步解密真相
在进一步深入分析前，我们结合基础篇03对 etcd 写流程原理的介绍（如下图），先大胆猜测下可能的原因。

![img](https://static001.geekbang.org/resource/image/8b/72/8b6dfa84bf8291369ea1803387906c72.png?wh=1920*1265)

猜测 1：etcd 集群出现分裂，三个节点分裂成两个集群。APIServer 配置的后端 etcd server 地址是三个节点，APIServer 并不会检查各节点集群 ID 是否一致，因此如果分裂，有可能会出现数据“消失”现象。这种故障之前在 Kubernetes 社区的确也见到过相关 issue，一般是变更异常导致的，显著特点是集群 ID 会不一致。猜测 2：Raft 日志同步异常，其他两个节点会不会因为 Raft 模块存在特殊 Bug 导致未收取到相关日志条目呢？这种怀疑我们可以通过 etcd 自带的 WAL 工具来判断，它可以显示 WAL 日志中收到的命令（流程四、五、六）。猜测 3：如果日志同步没问题，那有没有可能是 Apply 模块出现了问题，导致日志条目未被应用到 MVCC 模块呢（流程七）？猜测 4：若 Apply 模块执行了相关日志条目到 MVCC 模块，MVCC 模块的 treeIndex 子模块会不会出现了特殊 Bug， 导致更新失败（流程八）？猜测 5：若 MVCC 模块的 treeIndex 模块无异常，写请求到了 boltdb 存储模块，有没有可能 boltdb 出现了极端异常导致丢数据呢（流程九）？

带着以上怀疑和推测，让我们不断抽丝剥茧、去一步步探寻真相。首先还是从故障定位第一工具“日志”开始。我们查看 etcd 节点日志没发现任何异常日志，但是当查看 APIServer 日志的时候，发现持续报"required revision has been compacted"，这个错误根据我们基础篇 11 节介绍，我们知道原因一般是 APIServer 请求 etcd 版本号被压缩了。于是我们通过如下命令查看 etcd 节点详细的状态信息：

```
etcdctl endpoint status --cluster -w json | python -m 
json.tool
```
获得以下结果：

```
[
    {
        "Endpoint":"A"，
        "Status":{
            "header":{
                "cluster_id":17237436991929493444，
                "member_id":9372538179322589801，
                "raft_term":10，
                "revision":1052950
            }，
            "leader":9372538179322589801，
            "raftAppliedIndex":1098420，
            "raftIndex":1098430，
            "raftTerm":10，
            "version":"3.3.17"
        }
    }，
    {
        "Endpoint":"B"，
        "Status":{
            "header":{
                "cluster_id":17237436991929493444，
                "member_id":10501334649042878790，
                "raft_term":10，
                "revision":1025860
            }，
            "leader":9372538179322589801，
            "raftAppliedIndex":1098418，
            "raftIndex":1098428，
            "raftTerm":10，
            "version":"3.3.17"
        }
    }，
    {
        "Endpoint":"C"，
        "Status":{
            "header":{
                "cluster_id":17237436991929493444，
                "member_id":18249187646912138824，
                "raft_term":10，
                "revision":1028860
            }，
            "leader":9372538179322589801，
            "raftAppliedIndex":1098408，
            "raftIndex":1098428，
            "raftTerm":10，
            "version":"3.3.17"
        }
    }
]

```
从结果看，我们获得了如下信息：第一，集群未分裂，3 个节点 A、B、C cluster_id 都一致，集群分裂的猜测被排除。第二，初步判断集群 Raft 日志条目同步正常，raftIndex 表示 Raft 日志索引号，raftAppliedIndex 表示当前状态机应用的日志索引号。这两个核心字段显示三个节点相差很小，考虑到正在写入，未偏离正常范围，Raft 同步 Bug 导致数据丢失也大概率可以排除（不过最好还是用 WAL 工具验证下现在日志条目同步和写入 WAL 是否正常）。第三，观察三个节点的 revision 值，相互之间最大差距接近 30000，明显偏离标准值。在07中我给你深入介绍了 revision 的含义，它是 etcd 逻辑时钟，每次写入，就会全局递增。为什么三个节点之间差异如此之大呢？


接下来我们就一步步验证猜测、解密真相，猜测 1 集群分裂说被排除后，猜测 2Raft 日志同步异常也初步被我们排除了，那如何真正确认 Raft 日志同步正常呢？你可以使用下面这个方法验证 Raft 日志条目同步是否正常。首先我们写入一个值，比如 put hello 为 world，然后马上在各个节点上用 WAL 工具 etcd-dump-logs 搜索 hello。如下所示，各个节点上都可找到我们刚刚写入的命令。

```
$ etcdctl put hello world
OK
$ ./bin/tools/etcd-dump-logs ./Node1.etcd/ | grep hello
10         70 norm   header:<ID:3632562852862290438 > put:<key:"hello" value:"world" >
$ ./bin/tools/etcd-dump-logs ./Node2.etcd/ | grep hello
10         70 norm   header:<ID:3632562852862290438 > put:<key:"hello" value:"world" >
$ ./bin/tools/etcd-dump-logs ./Node3.etcd/ | grep hello
10         70 norm   header:<ID:3632562852862290438 > put:<key:"hello" value:"world" >
```
Raft 日志同步异常猜测被排除后，我们再看下会不会是 Apply 模块出现了问题。但是 raftAppliedIndex 却显示三个节点几乎无差异，那我们能不能通过这个指标来判断 Apply 流程是否正常呢？源码面前了无秘密，etcd 更新 raftAppliedIndex 核心代码如下所示，你会发现这个指标其实并不靠谱。Apply 流程出现逻辑错误时，并没重试机制。etcd 无论 Apply 流程是成功还是失败，都会更新 raftAppliedIndex 值。也就是一个请求在 Apply 或 MVCC 模块即便执行失败了，都依然会更新 raftAppliedIndex。

```
// ApplyEntryNormal apples an EntryNormal type Raftpb request to the EtcdServer
func （s *EtcdServer） ApplyEntryNormal（e *Raftpb.Entry） {
   shouldApplyV3 := false
   if e.Index > s.consistIndex.ConsistentIndex（） {
      // set the consistent index of current executing entry
      s.consistIndex.setConsistentIndex（e.Index）
      shouldApplyV3 = true
   }
   defer s.setAppliedIndex（e.Index）
   ....
 }
```
而三个节点 revision 差异偏离标准值，恰好又说明异常 etcd 节点可能未成功应用日志条目到 MVCC 模块。我们也可以通过查看 MVCC 的相关 metrics（比如 etcd_mvcc_put_total），来排除请求是否到了 MVCC 模块，事实是丢数据节点的 metrics 指标值的确远远落后正常节点。于是我们将真凶锁定在 Apply 流程上。我们对 Apply 流程在未向 MVCC 模块提交请求前可能提前返回的地方，都加了日志。同时我们查看 Apply 流程还发现，Apply 失败的时候并不会打印任何日志。这也解释了为什么出现了数据不一致严重错误，但三个 etcd 节点却并没有任何异常日志。为了方便定位问题，我们因此增加了 Apply 错误日志。同时我们测试发现，写入是否成功还跟 client 连接的节点有关，连接不同节点会出现不同的写入结果。我们用 debug 版本替换后，马上就输出了一条错误日志 auth: revision in header is old。

原来数据不一致是因为鉴权版本号不一致导致的，节点在 Apply 流程的时候，会判断 Raft 日志条目中的请求鉴权版本号是否小于当前鉴权版本号，如果小于就拒绝写入。那为什么各个节点的鉴权版本号会出现不一致呢？那就需要从可能修改鉴权版本号的源头分析。我们发现只有鉴权相关接口才会修改它，同时各个节点鉴权版本号之间差异已经固定不再增加，要成功解决就得再次复现。然后还了解到，当时 etcd 进程有过重启，我们怀疑会不会重启触发了什么 Bug，手动尝试复现一直失败。然而我们并未放弃，随后我们基于混沌工程，不断模拟真实业务场景、访问鉴权接口、注入故障（停止 etcd 进程等），最终功夫不负有心人，实现复现成功。真相终于浮出水面，原来当你无意间重启 etcd 的时候，如果最后一条命令是鉴权相关的，它并不会持久化 consistent index（KV 接口会持久化）。consistent index 在03里我们详细介绍了，它具有幂等作用，可防止命令重复执行。consistent index 的未持久化最终导致鉴权命令重复执行。恰好鉴权模块的 RoleGrantPermission 接口未实现幂等，重复执行会修改鉴权版本号。一连串的 Bug 最终导致鉴权号出现不一致，随后又放大成 MVCC 模块的 key-value 数据不一致，导致严重的数据毁坏。这个 Bug 影响 etcd v3 所有版本长达 3 年之久。查清楚问题后，我们也给社区提交了解决方案，合并到 master 后，同时 cherry-pick 到 etcd 3.3 和 3.4 稳定版本中。etcd v3.3.21 和 v3.4.8 后的版本已经修复此 Bug。

### 为什么会不一致
详细了解完这个案例的不一致后，我们再从本质上深入分析下为什么会出现不一致，以及还有哪些场景会导致类似问题呢？首先我们知道，etcd 各个节点数据一致性基于 Raft 算法的日志复制实现的，etcd 是个基于复制状态机实现的分布式系统。下图是分布式复制状态机原理架构，核心由 3 个组件组成，一致性模块、日志、状态机，其工作流程如下：

client 发起一个写请求（set x = 3）；server 向一致性模块（假设是 Raft）提交请求，一致性模块生成一个写提案日志条目。若 server 是 Leader，把日志条目广播给其他节点，并持久化日志条目到 WAL 中；当一半以上节点持久化日志条目后，Leader 的一致性模块将此日志条目标记为已提交（committed），并通知其他节点提交；server 从一致性模块获取已经提交的日志条目，异步应用到状态机持久化存储中（boltdb 等），然后返回给 client。

![img](https://static001.geekbang.org/resource/image/5c/4f/5c7a3079032f90120a6b309ee401fc4f.png?wh=605*319)



从图中我们可以了解到，在基于复制状态机实现的分布式存储系统中，Raft 等一致性算法它只能确保各个节点的日志一致性，也就是图中的流程二。而对于流程三来说，server 从日志里面获取已提交的日志条目，将其应用到状态机的过程，跟 Raft 算法本身无关，属于 server 本身的数据存储逻辑。

**也就是说有可能存在 server 应用日志条目到状态机失败，进而导致各个节点出现数据不一致。但是这个不一致并非 Raft 模块导致的，它已超过 Raft 模块的功能界限。**

比如在上面 Node 莫名其妙消失的案例中，就是应用日志条目到状态机流程中，出现逻辑错误，导致 key-value 数据未能持久化存储到 boltdb。这种逻辑错误即便重试也无法解决，目前社区也没有彻底的根治方案，只能根据具体案例进行针对性的修复。同时我给社区增加了 Apply 日志条目失败的警告日志。


### 其他典型不一致 Bug
还有哪些场景可能还会导致 Apply 流程失败呢？我再以一个之前升级 etcd 3.2 集群到 3.3 集群时，遇到的数据不一致的故障事件为例给你讲讲。这个故障对外的表现也是令人摸不着头脑，有服务不调度的、有 service 下的 endpoint 不更新的。最终我经过一番排查发现，原来数据不一致是由于 etcd 3.2 和 3.3 版本 Lease 模块的 Revoke Lease 行为不一致造成。etcd 3.2 版本的 RevokeLease 接口不需要鉴权，而 etcd 3.3 RevokeLease 接口增加了鉴权，因此当你升级 etcd 集群的时候，如果 etcd 3.3 版本收到了来自 3.2 版本的 RevokeLease 接口，就会导致因为没权限出现 Apply 失败，进而导致数据不一致，引发各种诡异现象。

除了重启 etcd、升级 etcd 可能会导致数据不一致，defrag 操作也可能会导致不一致。对一个 defrag 碎片整理来说，它是如何触发数据不一致的呢？ 触发的条件是 defrag 未正常结束时会生成 db.tmp 临时文件。这个文件可能包含部分上一次 defrag 写入的部分 key/value 数据，。而 etcd 下次 defrag 时并不会清理它，复用后就可能会出现各种异常场景，如重启后 key 增多、删除的用户数据 key 再次出现、删除 user/role 再次出现等。etcd 3.2.29、etcd 3.3.19、etcd 3.4.4 后的版本都已经修复这个 Bug。我建议你根据自己实际情况进行升级，否则踩坑后，数据不一致的修复工作是非常棘手的，风险度极高。

从以上三个案例里，我们可以看到，算法一致性不代表一个庞大的分布式系统工程实现中一定能保障一致性，工程实现上充满着各种挑战，从不可靠的网络环境到时钟、再到人为错误、各模块间的复杂交互等，几乎没有一个存储系统能保证任意分支逻辑能被测试用例 100% 覆盖。复制状态机在给我们带来数据同步的便利基础上，也给我们上层逻辑开发提出了高要求。也就是说任何接口逻辑变更 etcd 需要保证兼容性，否则就很容易出现 Apply 流程失败，导致数据不一致。同时除了 Apply 流程可能导致数据不一致外，我们从 defrag 案例中也看到了一些维护变更操作，直接针对底层存储模块 boltdb 的，也可能会触发 Bug，导致数据不一致。


### 最佳实践
在了解了 etcd 数据不一致的风险和原因后，我们在实践中有哪些方法可以提前发现和规避不一致问题呢？下面我为你总结了几个最佳实践，它们分别是：

开启 etcd 的数据毁坏检测功能；应用层的数据一致性检测；定时数据备份；良好的运维规范（比如使用较新稳定版本、确保版本一致性、灰度变更）。



### 开启 etcd 的数据毁坏检测功能
首先和你介绍下 etcd 的数据毁坏检测功能。etcd 不仅支持在启动的时候，通过 --experimental-initial-corrupt-check 参数检查各个节点数据是否一致，也支持在运行过程通过指定 --experimental-corrupt-check-time 参数每隔一定时间检查数据一致性。那么它的一致性检测原理是怎样的？如果出现不一致性，etcd 会采取什么样动作去降低数据不一致影响面呢？其实我们无非就是想确定 boltdb 文件里面的内容跟其他节点内容是否一致。因此我们可以枚举所有 key value，然后比较即可。etcd 的实现也就是通过遍历 treeIndex 模块中的所有 key 获取到版本号，然后再根据版本号从 boltdb 里面获取 key 的 value，使用 crc32 hash 算法，将 bucket name、key、value 组合起来计算它的 hash 值。如果你开启了 --experimental-initial-corrupt-check，启动的时候每个节点都会去获取 peer 节点的 boltdb hash 值，然后相互对比，如果不相等就会无法启动。

而定时检测是指 Leader 节点获取它当前最新的版本号，并通过 Raft 模块的 ReadIndex 机制确认 Leader 身份。当确认完成后，获取各个节点的 revision 和 boltdb hash 值，若出现 Follower 节点的 revision 大于 Leader 等异常情况时，就可以认为不一致，发送 corrupt 告警，触发集群 corruption 保护，拒绝读写。从 etcd 上面的一致性检测方案我们可以了解到，目前采用的方案是比较简单、暴力的。因此可能随着数据规模增大，出现检测耗时增大等扩展性问题。而 DynamoDB 等使用了 merkle tree 来实现增量 hash 检测，这也是 etcd 未来可能优化的一个方向。最后你需要特别注意的是，etcd 数据毁坏检测的功能目前还是一个试验 (experimental) 特性，在比较新的版本才趋于稳定、成熟（推荐 v3.4.9 以上），预计在未来的 etcd 3.5 版本中才会变成稳定特性，因此 etcd 3.2/3.3 系列版本就不能使用此方案。


### 应用层的数据一致性检测
那要如何给 etcd 3.2/3.3 版本增加一致性检测呢? 其实除了 etcd 自带数据毁坏检测，我们还可以通过在应用层通过一系列方法来检测数据一致性，它们适用于 etcd 所有版本。接下来我给你讲讲应用层检测的原理。从上面我们对数据不一致性案例的分析中，我们知道数据不一致在 MVCC、boltdb 会出现很多种情况，比如说 key 数量不一致、etcd 逻辑时钟版本号不一致、MVCC 模块收到的 put 操作 metrics 指标值不一致等等。因此我们的应用层检测方法就是基于它们的差异进行巡检。首先针对 key 数量不一致的情况，我们可以实现巡检功能，定时去统计各个节点的 key 数，这样可以快速地发现数据不一致，从而及时介入，控制数据不一致影响，降低风险。在你统计节点 key 数时，记得查询的时候带上 WithCountOnly 参数。etcd 从 treeIndex 模块获取到 key 数后就及时返回了，无需访问 boltdb 模块。如果你的数据量非常大（涉及到百万级别），那即便是从 treeIndex 模块返回也会有一定的内存开销，因为它会把 key 追加到一个数组里面返回。

而在 WithCountOnly 场景中，我们只需要统计 key 数即可。因此我给社区提了优化方案，目前已经合并到 master 分支。对百万级别的 key 来说，WithCountOnly 时内存开销从数 G 到几乎零开销，性能也提升数十倍。其次我们可以基于 endpoint 各个节点的 revision 信息做一致性监控。一般情况下，各个节点的差异是极小的。最后我们还可以基于 etcd MVCC 的 metrics 指标来监控。比如上面提到的 mvcc_put_total，理论上每个节点这些 MVCC 指标是一致的，不会出现偏离太多。



### 定时数据备份
etcd 数据不一致的修复工作极其棘手。发生数据不一致后，各个节点可能都包含部分最新数据和脏数据。如果最终我们无法修复，那就只能使用备份数据来恢复了。因此备份特别重要，备份可以保障我们在极端场景下，能有保底的机制去恢复业务。请记住，在做任何重要变更前一定先备份数据，以及在生产环境中建议增加定期的数据备份机制（比如每隔 30 分钟备份一次数据）。你可以使用开源的 etcd-operator 中的 backup-operator 去实现定时数据备份，它可以将 etcd 快照保存在各个公有云的对象存储服务里面。



### 良好的运维规范
最后我给你介绍几个运维规范，这些规范可以帮助我们尽量少踩坑（即便你踩坑后也可以控制故障影响面）。首先是确保集群中各节点 etcd 版本一致。若各个节点的版本不一致，因各版本逻辑存在差异性，这就会增大触发不一致 Bug 的概率。比如我们前面提到的升级版本触发的不一致 Bug 就属于此类问题。其次是优先使用较新稳定版本的 etcd。像上面我们提到的 3 个不一致 Bug，在最新的 etcd 版本中都得到了修复。你可以根据自己情况进行升级，以避免下次踩坑。同时你可根据实际业务场景以及安全风险，来评估是否有必要开启鉴权，开启鉴权后涉及的逻辑更复杂，有可能增大触发数据不一致 Bug 的概率。最后是你在升级 etcd 版本的时候，需要多查看 change log，评估是否存在可能有不兼容的特性。在你升级集群的时候注意先在测试环境多验证，生产环境务必先灰度、再全量。


### 小结
最后，我来总结下我们今天的内容。我从消失的 Node 案例为例，介绍了 etcd 中定位一个复杂不一致问题的思路和方法工具。核心就是根据我们对 etcd 读写原理的了解，对每个模块可能出现的问题进行大胆猜想。同时我们要善于借助日志、metrics、etcd tool 等进行验证排除。定位到最终模块问题后，如果很难复现，我们可以借助混沌工程等技术注入模拟各类故障。遇到复杂 Bug 时，请永远不要轻言放弃，它一定是一个让你快速成长的机会。其次我介绍了 etcd 数据不一致的核心原因：Raft 算法只能保证各个节点日志同步的一致性，但 Apply 流程是异步的，它从一致性模块获取日志命令，应用到状态机的准确性取决于业务逻辑，这块是没有机制保证的。同时，defrag 等运维管理操作，会直接修改底层存储数据，异常场景处理不严谨也会导致数据不一致。数据不一致的风险是非常大的，轻则业务逻辑异常，重则核心数据丢失。我们需要机制去提前发现和规避它，因此最后我详细给你总结了 etcd 本身和应用层的一致性监控、定时备份数据、良好的运维规范等若干最佳实践，这些都是宝贵的实践总结，希望你能有所收获。


## 13 | db大小：为什么etcd社区建议db大小不超过8G？

在03写流程中我和你分享了 etcd Quota 模块，那么 etcd 为什么需要对 db 增加 Quota 限制，以及不建议你的 etcd 集群 db 大小超过 8G 呢？ 过大的 db 文件对集群性能和稳定性有哪些影响？今天我要和你分享的主题就是关于 db 大小。我将通过一个大数据量的 etcd 集群为案例，为你剖析 etcd db 大小配额限制背后的设计思考和过大的 db 潜在隐患。希望通过这节课，帮助你理解大数据量对集群的各个模块的影响，配置合理的 db Quota 值。同时，帮助你在实际业务场景中，遵循最佳实践，尽量减少 value 大小和大 key-value 更新频率，避免 db 文件大小不断增长。


### 分析整体思路
为了帮助你直观地理解大数据量对集群稳定性的影响，我首先将为你写入大量数据，构造一个 db 大小为 14G 的大集群。然后通过此集群为你分析 db 大小的各个影响面，db 大小影响面如下图所示。

![img](https://static001.geekbang.org/resource/image/ab/11/ab657951310461c835963c38e43fdc11.png?wh=1920*685)



首先是启动耗时。etcd 启动的时候，需打开 boltdb db 文件，读取 db 文件所有 key-value 数据，用于重建内存 treeIndex 模块。因此在大量 key 导致 db 文件过大的场景中，这会导致 etcd 启动较慢。其次是节点内存配置。etcd 在启动的时候会通过 mmap 将 db 文件映射内存中，若节点可用内存不足，小于 db 文件大小时，可能会出现缺页文件中断，导致服务稳定性、性能下降。接着是 treeIndex 索引性能。因 etcd 不支持数据分片，内存中的 treeIndex 若保存了几十万到上千万的 key，这会增加查询、修改操作的整体延时。然后是 boltdb 性能。大 db 文件场景会导致事务提交耗时增长、抖动。再次是集群稳定性。大 db 文件场景下，无论你是百万级别小 key 还是上千个大 value 场景，一旦出现 expensive request 后，很容易导致 etcd OOM、节点带宽满而丢包。最后是快照。当 Follower 节点落后 Leader 较多数据的时候，会触发 Leader 生成快照重建发送给 Follower 节点，Follower 基于它进行还原重建操作。较大的 db 文件会导致 Leader 发送快照需要消耗较多的 CPU、网络带宽资源，同时 Follower 节点重建还原慢。




### 构造大集群
简单介绍完 db 大小的六个影响面后，我们下面来构造一个大数据量的集群，用于后续各个影响面的分析。首先，我通过一系列如下benchmark命令，向一个 8 核 32G 的 3 节点的集群写入 120 万左右 key。key 大小为 32，value 大小为 256 到 10K，用以分析大 db 集群案例中的各个影响面。

```
./benchmark put --key-size 32 --val-size 10240 --total 
1000000 --key-space-size 2000000 --clients 50 --conns 50
```
执行完一系列 benchmark 命令后，db size 达到 14G，总 key 数达到 120 万，其监控如下图所示：

![img](https://static001.geekbang.org/resource/image/67/60/67aa0c0fe078byy681fe4c55a3983f60.png?wh=1338*362)

![img](https://static001.geekbang.org/resource/image/33/88/331ac3c759578b297546f1651385be88.png?wh=1314*412)


### 启动耗时
在如上的集群中，我通过 benchmark 工具将 etcd 集群 db 大小压测到 14G 后，在重新启动 etcd 进程的时候，如下日志所示，你会发现启动比较慢，为什么大 db 文件会影响 etcd 启动耗时呢？

```
2021-02-15 02:25:55.273712 I | etcdmain: etcd Version: 3.4.9
2021-02-15 02:26:58.806882 I | etcdserver: recovered store from snapshot at index 2100090
2021-02-15 02:26:58.808810 I | mvcc: restore compact to 1000002
2021-02-15 02:27:19.120141 W | etcdserver: backend quota 26442450944 exceeds maximum recommended quota 8589934592
2021-02-15 02:27:19.297363 I | embed: ready to serve client requests
```

通过对 etcd 启动流程增加耗时统计，我们可以发现核心瓶颈主要在于打开 db 文件和重建内存 treeIndex 模块。这里我重点先和你介绍下 etcd 启动后，重建内存 treeIndex 的原理。我们知道 treeIndex 模块维护了用户 key 与 boltdb key 的映射关系，boltdb 的 key、value 又包含了构建 treeIndex 的所需的数据。因此 etcd 启动的时候，会启动不同角色的 goroutine 并发完成 treeIndex 构建。

首先是主 goroutine。它的职责是遍历 boltdb，获取所有 key-value 数据，并将其反序列化成 etcd 的 mvccpb.KeyValue 结构。核心原理是基于 etcd 存储在 boltdb 中的 key 数据有序性，按版本号从 1 开始批量遍历，每次查询 10000 条 key-value 记录，直到查询数据为空。其次是构建 treeIndex 索引的 goroutine。它从主 goroutine 获取 mvccpb.KeyValue 数据，基于 key、版本号、是否带删除标识等信息，构建 keyIndex 对象，插入到 treeIndex 模块的 B-tree 中。因可能存在多个 goroutine 并发操作 treeIndex，treeIndex 的 Insert 函数会加全局锁，如下所示。etcd 启动时只有一个构建 treeIndex 索引的 goroutine，因此 key 多时，会比较慢。之前我尝试优化成多 goroutine 并发构建，但是效果不佳，大量耗时会消耗在此锁上。

```
func (ti *treeIndex) Insert(ki *keyIndex) {
   ti.Lock()
   defer ti.Unlock()
   ti.tree.ReplaceOrInsert(ki)
}
```

### 节点内存配置
etcd 进程重启完成后，在没任何读写 QPS 情况下，如下所示，你会发现 etcd 所消耗的内存比 db 大小还大一点。这又是为什么呢？如果 etcd db 文件大小超过节点内存规格，会导致什么问题吗？

![img](https://static001.geekbang.org/resource/image/02/a1/027ef8e1759a2800f1a2c1c105d7d7a1.png?wh=1312*394)



在10介绍 boltdb 存储原理的时候，我和你分享过 boltdb 文件的磁盘布局结构和其对外提供的 API 原理。etcd 在启动的时候，会通过 boltdb 的 Open API 获取数据库对象，而 Open API 它会通过 mmap 机制将 db 文件映射到内存中。由于 etcd 调用 boltdb Open API 的时候，设置了 mmap 的 MAP_POPULATE flag，它会告诉 Linux 内核预读文件，将 db 文件内容全部从磁盘加载到物理内存中。因此在你节点内存充足的情况下，启动后你看到的 etcd 占用内存，一般是 db 文件大小与内存 treeIndex 之和。在节点内存充足的情况下，启动后，client 后续发起对 etcd 的读操作，可直接通过内存获取 boltdb 的 key-value 数据，不会产生任何磁盘 IO，具备良好的读性能、稳定性。而当你的 db 文件大小超过节点内存配置时，若你查询的 key 所相关的 branch page、leaf page 不在内存中，那就会触发主缺页中断，导致读延时抖动、QPS 下降。因此为了保证 etcd 集群性能的稳定性，我建议你的 etcd 节点内存规格要大于你的 etcd db 文件大小。


### treeIndex
当我们往集群中写入了一百多万 key 时，此时你再读取一个 key 范围操作的延时会出现一定程度上升，这是为什么呢？我们该如何分析耗时是在哪一步导致的？在 etcd 3.4 中提供了 trace 特性，它可帮助我们定位、分析请求耗时过长问题。不过你需要特别注意的是，此特性在 etcd 3.4 中，因为依赖 zap logger，默认为关闭。你可以通过设置 etcd 启动参数中的 --logger=zap 来开启。开启之后，我们可以在 etcd 日志中找到类似如下的耗时记录。

```
{
"msg":"trace[331581563] range"，
"detail":"{range_begin:/vip/a; range_end:/vip/b; response_count:19304; response_revision:1005564; }"，
"duration":"146.432768ms"，
"steps":[
"trace[331581563] 'range keys from in-memory treeIndex'  (duration: 95.925033ms)"，
"trace[331581563] 'range keys from bolt db'  (duration: 47.932118ms)"
]
```
此日志记录了查询请求"etcdctl get --prefix /vip/a"。它在 treeIndex 中查询相关 key 耗时 95ms，从 boltdb 遍历 key 时 47ms。主要原因还是此查询涉及的 key 数较多，高达一万九。也就是说若 treeIndex 中存储了百万级的 key 时，它可能也会产生几十毫秒到数百毫秒的延时，对于期望业务延时稳定在较小阈值内的业务，就无法满足其诉求。

### boltdb 性能
当 db 文件大小持续增长到 16G 乃至更大后，从 etcd 事务提交监控 metrics 你可能会观察到，boltdb 在提交事务时偶尔出现了较高延时，那么延时是怎么产生的呢？在10介绍 boltdb 的原理时，我和你分享了 db 文件的磁盘布局，它是由 meta page、branch page、leaf page、free list、free 页组成的。同时我给你介绍了 boltdb 事务提交的四个核心流程，分别是 B+ tree 的重平衡、分裂，持久化 dirty page，持久化 freelist 以及持久化 meta data。事务提交延时抖动的原因主要是在 B+ tree 树的重平衡和分裂过程中，它需要从 freelist 中申请若干连续的 page 存储数据，或释放空闲的 page 到 freelist。freelist 后端实现在 boltdb 中是 array。当申请一个连续的 n 个 page 存储数据时，它会遍历 boltdb 中所有的空闲页，直到找到连续的 n 个 page。因此它的时间复杂度是 O(N)。若 db 文件较大，又存在大量的碎片空闲页，很可能导致超时。

同时事务提交过程中，也可能会释放若干个 page 给 freelist，因此需要合并到 freelist 的数组中，此操作时间复杂度是 O(NLog N)。假设我们 db 大小 16G，page size 4KB，则有 400 万个 page。经过各种修改、压缩后，若存在一半零散分布的碎片空闲页，在最坏的场景下，etcd 每次事务提交需要遍历 200 万个 page 才能找到连续的 n 个 page，同时还需要持久化 freelist 到磁盘。为了优化 boltdb 事务提交的性能，etcd 社区在 bbolt 项目中，实现了基于 hashmap 来管理 freelist。通过引入了如下的三个 map 数据结构（freemaps 的 key 是连续的页数，value 是以空闲页的起始页 pgid 集合，forwardmap 和 backmap 用于释放的时候快速合并页），将申请和释放时间复杂度降低到了 O(1)。freelist 后端实现可以通过 bbolt 的 FreeListType 参数来控制，支持 array 和 hashmap。在 etcd 3.4 版本中目前还是 array，未来的 3.5 版本将默认是 hashmap。

```
freemaps       map[uint64]pidSet           // key is the size of continuous pages(span)，value is a set which contains the starting pgids of same size
forwardMap     map[pgid]uint64             // key is start pgid，value is its span size
backwardMap    map[pgid]uint64             // key is end pgid，value is its span size
```
另外在 db 中若存在大量空闲页，持久化 freelist 需要消耗较多的 db 大小，并会导致额外的事务提交延时。若未持久化 freelist，bbolt 支持通过重启时扫描全部 page 来构造 freelist，降低了 db 大小和提升写事务提交的性能（但是它会带来 etcd 启动延时的上升）。此行为可以通过 bbolt 的 NoFreelistSync 参数来控制，默认是 true 启用此特性。


### 集群稳定性
db 文件增大后，另外一个非常大的隐患是用户 client 发起的 expensive request，容易导致集群出现各种稳定性问题。本质原因是 etcd 不支持数据分片，各个节点保存了所有 key-value 数据，同时它们又存储在 boltdb 的一个 bucket 里面。当你的集群含有百万级以上 key 的时候，任意一种 expensive read 请求都可能导致 etcd 出现 OOM、丢包等情况发生。那么有哪些 expensive read 请求会导致 etcd 不稳定性呢？首先是简单的 count only 查询。如下图所示，当你想通过 API 统计一个集群有多少 key 时，如果你的 key 较多，则有可能导致内存突增和较大的延时。

![img](https://static001.geekbang.org/resource/image/44/a1/44ee247e9a31a455aca28459e5bb45a1.png?wh=1322*418)

在 etcd 3.5 版本之前，统计 key 数会遍历 treeIndex，把 key 追加到数组中。然而当数据规模较大时，追加 key 到数组中的操作会消耗大量内存，同时数组扩容时涉及到大量数据拷贝，会导致延时上升。

其次是 limit 查询。当你只想查询若干条数据的时候，若你的 key 较多，也会导致类似 count only 查询的性能、稳定性问题。原因是 etcd 3.5 版本之前遍历 index B-tree 时，并未将 limit 参数下推到索引层，导致了无用的资源和时间消耗。优化方案也很简单，etcd 3.5 中我提的优化 PR 将 limit 参数下推到了索引层，实现查询性能百倍提升。

最后是大包查询。当你未分页批量遍历 key-value 数据或单 key-value 数据较大的时候，随着请求 QPS 增大，etcd OOM、节点出现带宽瓶颈导致丢包的风险会越来越大。


问题主要由以下两点原因导致：第一，etcd 需要遍历 treeIndex 获取 key 列表。若你未分页，一次查询万级 key，显然会消耗大量内存并且高延时。第二，获取到 key 列表、版本号后，etcd 需要遍历 boltdb，将 key-value 保存到查询结果数据结构中。如下 trace 日志所示，一个请求可能在遍历 boltdb 时花费很长时间，同时可能会消耗几百 M 甚至数 G 的内存。随着请求 QPS 增大，极易出现 OOM、丢包等。etcd 这块未来的优化点是实现流式传输。

```
{
"level":"info",
"ts":"2021-02-15T03:44:52.209Z",
"caller":"traceutil/trace.go:145",
"msg":"trace[1908866301] range",
"detail":"{range_begin:; range_end:; response_count:1232274; response_revision:3128500; }",
"duration":"9.063748801s",
"start":"2021-02-15T03:44:43.145Z",
"end":"2021-02-15T03:44:52.209Z",
"steps":[
"trace[1908866301] 'range keys from in-memory index tree' (duration: 693.262565ms)",
"trace[1908866301] 'range keys from bolt db' (duration: 8.22558566s)",
"trace[1908866301] 'assemble the response' (duration: 18.810315ms)"
]
}
```


### 快照
大 db 文件最后一个影响面是快照。它会影响 db 备份文件生成速度、Leader 发送快照给 Follower 节点的资源开销、Follower 节点通过快照重建恢复的速度。我们知道 etcd 提供了快照功能，帮助我们通过 API 即可备份 etcd 数据。当 etcd 收到 snapshot 请求的时候，它会通过 boltdb 接口创建一个只读事务 Tx，随后通过事务的 WriteTo 接口，将 meta page 和 data page 拷贝到 buffer 即可。但是随着 db 文件增大，快照事务执行的时间也会越来越长，而长事务则会导致 db 文件大小发生显著增加。也就是说当 db 大时，生成快照不仅慢，生成快照时可能还会触发 db 文件大小持续增长，最终达到配额限制。为什么长事务可能会导致 db 大小增长呢？ 这个问题我先将它作为思考题，你可以分享一下你的想法，后续我将为你详细解答。


快照的另一大作用是当 Follower 节点异常的时候，Leader 生成快照发送给 Follower 节点，Follower 使用快照重建并追赶上 Leader。此过程涉及到一定的 CPU、内存、网络带宽等资源开销。同时，若快照和集群写 QPS 较大，Leader 发送快照给 Follower 和 Follower 应用快照到状态机的流程会耗费较长的时间，这可能会导致基于快照重建后的 Follower 依然无法通过正常的日志复制模式来追赶 Leader，只能继续触发 Leader 生成快照，进而进入死循环，Follower 一直处于异常中。


### 小结
最后我们来小结下今天的内容。大 db 文件首先会影响 etcd 启动耗时，因为 etcd 需要打开 db 文件，初始化 db 对象，并遍历 boltdb 中的所有 key-value 以重建内存 treeIndex。其次，较大 db 文件会导致 etcd 依赖更高配置的节点内存规格，etcd 通过 mmap 将 db 文件映射到内存中。etcd 启动后，正常情况下读 etcd 过程不涉及磁盘 IO，若节点内存不够，可能会导致缺页中断，引起延时抖动、服务性能下降。接着 treeIndex 维护了所有 key 的版本号信息，当 treeIndex 中含有百万级 key 时，在 treeIndex 中搜索指定范围的 key 的开销是不能忽略的，此开销可能高达上百毫秒。然后当 db 文件过大后，boltdb 本身连续空闲页的申请、释放、存储都会存在一定的开销。etcd 社区已通过新的 freelist 管理数据结构 hashmap 对其进行优化，将时间复杂度降低到了 O(1)，同时支持事务提交时不持久化 freelist，而是通过重启时扫描 page 重建，以提升 etcd 写性能、降低 db 大小。

随后我给你介绍了 db 文件过大后，count only、limit、大包查询等 expensive request 对集群稳定性的影响。建议你的业务尽量避免任何 expensive request 请求。最后我们介绍了大 db 文件对快照功能的影响。大 db 文件意味着更长的备份时间，而更长的只读事务则可能会导致 db 文件增长。同时 Leader 发送快照与 Follower 基于快照重建都需要较长时间，在集群写请求较大的情况下，可能会陷入死循环，导致落后的 Follower 节点一直无法追赶上 Leader。


## 14 | 延时：为什么你的etcd请求会出现超时？

在使用 etcd 的过程中，你是否被日志中的"apply request took too long"和“etcdserver: request timed out"等高延时现象困扰过？它们是由什么原因导致的呢？我们应该如何来分析这些问题？这就是我今天要和你分享的主题：etcd 延时。希望通过这节课，帮助你掌握 etcd 延时抖动、超时背后的常见原因和分析方法，当你遇到类似问题时，能独立定位、解决。同时，帮助你在实际业务场景中，合理配置集群，遵循最佳实践，尽量减少 expensive request，避免 etcd 请求出现超时。


### 分析思路及工具
首先，当我们面对一个高延时的请求案例后，如何梳理问题定位思路呢？知彼知己，方能百战不殆，定位问题也是类似。首先我们得弄清楚产生问题的原理、流程，在02、03、04中我已为你介绍过读写请求的核心链路。其次是熟练掌握相关工具，借助它们，可以帮助我们快速攻破疑难杂症。这里我们再回顾下 03 中介绍的，Leader 收到一个写请求，将一个日志条目复制到集群多数节点并应用到存储状态机的流程（如下图所示），通过此图我们看看写流程上哪些地方可能会导致请求超时呢？

![img](https://static001.geekbang.org/resource/image/df/2c/df9yy18a1e28e18295cfc15a28cd342c.png?wh=1920*1328)

首先是流程四，一方面，Leader 需要并行将消息通过网络发送给各 Follower 节点，依赖网络性能。另一方面，Leader 需持久化日志条目到 WAL，依赖磁盘 I/O 顺序写入性能。其次是流程八，应用日志条目到存储状态机时，etcd 后端 key-value 存储引擎是 boltdb。正如我们10所介绍的，它是一个基于 B+ tree 实现的存储引擎，当你写入数据，提交事务时，它会将 dirty page 持久化到磁盘中。在这过程中 boltdb 会产生磁盘随机 I/O 写入，因此事务提交性能依赖磁盘 I/O 随机写入性能。最后，在整个写流程处理过程中，etcd 节点的 CPU、内存、网络带宽资源应充足，否则肯定也会影响性能。初步了解完可能导致延时抖动的瓶颈处之后，我给你总结了 etcd 问题定位过程中常用的工具，你可以参考下面这幅图。

![img](https://static001.geekbang.org/resource/image/b5/fc/b5bb69c8effda97f2ef78b067ab1aafc.png?wh=1920*1300)



图的左边是读写请求链路中可能出现瓶颈或异常的点，比如上面流程分析中提到的磁盘、内存、CPU、网络资源。图的右边是常用的工具，分别是 metrics、trace 日志、etcd 其他日志、WAL 及 boltdb 分析工具等。接下来，我基于读写请求的核心链路和其可能出现的瓶颈点，结合相关的工具，为你深入分析 etcd 延时抖动的定位方法和原因。



### 网络
首先我们来看看流程图中第一个提到可能瓶颈点，网络模块。在 etcd 中，各个节点之间需要通过 2380 端口相互通信，以完成 Leader 选举、日志同步等功能，因此底层网络质量（吞吐量、延时、稳定性）对上层 etcd 服务的性能有显著影响。网络资源出现异常的常见表现是连接闪断、延时抖动、丢包等。那么我们要如何定位网络异常导致的延时抖动呢？一方面，我们可以使用常规的 ping/traceroute/mtr、ethtool、ifconfig/ip、netstat、tcpdump 网络分析工具等命令，测试网络的连通性、延时，查看网卡的速率是否存在丢包等错误，确认 etcd 进程的连接状态及数量是否合理，抓取 etcd 报文分析等。另一方面，etcd 应用层提供了节点之间网络统计的 metrics 指标，分别如下：

etcd_network_active_peer，表示 peer 之间活跃的连接数；etcd_network_peer_round_trip_time_seconds，表示 peer 之间 RTT 延时；etcd_network_peer_sent_failures_total，表示发送给 peer 的失败消息数；etcd_network_client_grpc_sent_bytes_total，表示 server 发送给 client 的总字节数，通过这个指标我们可以监控 etcd 出流量；etcd_network_client_grpc_received_bytes_total，表示 server 收到 client 发送的总字节数，通过这个指标可以监控 etcd 入流量。

client 入流量监控如下图所示：

![img](https://static001.geekbang.org/resource/image/26/ff/26617a4c08e7c1e155c4332058451cff.png?wh=866*356)

client 出流量如下图监控所示。 从图中你可以看到，峰值接近 140MB/s(1.12Gbps)，这是非常不合理的，说明业务中肯定有大量 expensive read request 操作。若 etcd 集群读写请求开始出现超时，你可以用 ifconfig 等命令查看是否出现丢包等错误。

![img](https://static001.geekbang.org/resource/image/4c/0b/4c8659e621305200b8f761b1e319460b.png?wh=856*356)

etcd metrics 指标名由 namespace 和 subsystem、name 组成。namespace 为 etcd， subsystem 是模块名（比如 network、name 具体的指标名）。你可以在 Prometheus 里搜索 etcd_network 找到所有 network 相关的 metrics 指标名。下面是一个集群中某节点异常后的 metrics 指标：



```
etcd_network_active_peers{Local="fd422379fda50e48"，Remote="8211f1d0f64f3269"} 1
etcd_network_active_peers{Local="fd422379fda50e48"，Remote="91bc3c398fb3c146"} 0
etcd_network_peer_sent_failures_total{To="91bc3c398fb3c146"} 47774
etcd_network_client_grpc_sent_bytes_total 513207
```
从以上 metrics 中，你可以看到 91bc3c398fb3c146 节点出现了异常。在 etcd 场景中，网络质量导致 etcd 性能下降主要源自两个方面：一方面，expensive request 中的大包查询会使网卡出现瓶颈，产生丢包等错误，从而导致 etcd 吞吐量下降、高延时。expensive request 导致网卡丢包，出现超时，这在 etcd 中是非常典型且易发生的问题，它主要是因为业务没有遵循最佳实践，查询了大量 key-value。另一方面，在跨故障域部署的时候，故障域可能是可用区、城市。故障域越大，容灾级别越高，但各个节点之间的 RTT 越高，请求的延时更高。


### 磁盘 I/O
了解完网络问题的定位方法和导致网络性能下降的因素后，我们再看看最核心的磁盘 I/O。正如我在开头的 Raft 日志复制整体流程图中和你介绍的，在 etcd 中无论是 Raft 日志持久化还是 boltdb 事务提交，都依赖于磁盘 I/O 的性能。当 etcd 请求延时出现波动时，我们往往首先关注 disk 相关指标是否正常。我们可以通过 etcd 磁盘相关的 metrics(etcd_disk_wal_fsync_duration_seconds 和 etcd_disk_backend_commit_duration_seconds) 来观测应用层数据写入磁盘的性能。etcd_disk_wal_fsync_duration_seconds（简称 disk_wal_fsync）表示 WAL 日志持久化的 fsync 系统调用延时数据。一般本地 SSD 盘 P99 延时在 10ms 内，如下图所示。

![img](https://static001.geekbang.org/resource/image/9a/52/9a08490980abb23f90d8e59a83543e52.png?wh=1326*352)

etcd_disk_backend_commit_duration_seconds（简称 disk_backend_commit）表示后端 boltdb 事务提交的延时，一般 P99 在 120ms 内。

![img](https://static001.geekbang.org/resource/image/29/db/294600a0a144be38e9d7b69d9403f3db.png?wh=1338*356)

这里你需要注意的是，一般监控显示的磁盘延时都是 P99，但实际上 etcd 对磁盘特别敏感，一次磁盘 I/O 波动就可能产生 Leader 切换。如果你遇到集群 Leader 出现切换、请求超时，但是磁盘指标监控显示正常，你可以查看 P100 确认下是不是由于磁盘 I/O 波动导致的。同时 etcd 的 WAL 模块在 fdatasync 操作超过 1 秒时，也会在 etcd 中打印如下的日志，你可以结合日志进一步定位。

```
if took > warnSyncDuration {
   if w.lg != nil {
      w.lg.Warn(
         "slow fdatasync",
         zap.Duration("took", took),
         zap.Duration("expected-duration", warnSyncDuration),
      )
   } else {
      plog.Warningf("sync duration of %v, expected less than %v", took, warnSyncDuration)
   }
}

```
当 disk_wal_fsync 指标异常的时候，一般是底层硬件出现瓶颈或异常导致。当然也有可能是 CPU 高负载、cgroup blkio 限制导致的，我们具体应该如何区分呢？你可以通过 iostat、blktrace 工具分析瓶颈是在应用层还是内核层、硬件层。其中 blktrace 是 blkio 层的磁盘 I/O 分析利器，它可记录 IO 进入通用块层、IO 请求生成插入请求队列、IO 请求分发到设备驱动、设备驱动处理完成这一系列操作的时间，帮助你发现磁盘 I/O 瓶颈发生的阶段。当 disk_backend_commit 指标的异常时候，说明事务提交过程中的 B+ tree 树重平衡、分裂、持久化 dirty page、持久化 meta page 等操作耗费了大量时间。disk_backend_commit 指标异常，能说明是磁盘 I/O 发生了异常吗？


若 disk_backend_commit 较高、disk_wal_fsync 却正常，说明瓶颈可能并非来自磁盘 I/O 性能，也许是 B+ tree 的重平衡、分裂过程中的较高时间复杂度逻辑操作导致。比如 etcd 目前所有 stable 版本（etcd 3.2 到 3.4），从 freelist 中申请和回收若干连续空闲页的时间复杂度是 O(N)，当 db 文件较大、空闲页碎片化分布的时候，则可能导致事务提交高延时。那如何区分事务提交过程中各个阶段的耗时呢？

etcd 还提供了 disk_backend_commit_rebalance_duration 和disk_backend_commit_spill_duration 两个 metrics，分别表示事务提交过程中 B+ tree 的重平衡和分裂操作耗时分布区间。最后，你需要注意 disk_wal_fsync 记录的是 WAL 文件顺序写入的持久化时间，disk_backend_commit 记录的是整个事务提交的耗时。后者涉及的磁盘 I/O 是随机的，为了保证你 etcd 集群的稳定性，建议使用 SSD 磁盘以确保事务提交的稳定性。


### expensive request
若磁盘和网络指标都很正常，那么延时高还有可能是什么原因引起的呢？从02介绍的读请求链路我们可知，一个读写请求经过 Raft 模块处理后，最终会走到 MVCC 模块。那么在 MVCC 模块会有哪些场景导致延时抖动呢？时间耗在哪个处理流程上了？etcd 3.4 版本之前，在应用 put/txn 等请求到状态机的 apply 和处理读请求 range 流程时，若一个请求执行超过 100ms 时，默认会在 etcd log 中打印一条"apply request took too long"的警告日志。通过此日志我们可以知道集群中 apply 流程产生了较慢的请求，但是不能确定具体是什么因素导致的。比如在 Kubernetes 中，当集群 Pod 较多的时候，若你频繁执行 List Pod，可能会导致 etcd 出现大量的"apply request took too long"警告日志。

因为对 etcd 而言，List Pod 请求涉及到大量的 key 查询，会消耗较多的 CPU、内存、网络资源，此类 expensive request 的 QPS 若较大，则很可能导致 OOM、丢包。当然，除了业务发起的 expensive request 请求导致延时抖动以外，也有可能是 etcd 本身的设计实现存在瓶颈。比如在 etcd 3.2 和 3.3 版本写请求完成之前，需要更新 MVCC 的 buffer，进行升级锁操作。然而此时若集群中出现了一个 long expensive read request，则会导致写请求执行延时抖动。因为 expensive read request 事务会一直持有 MVCC 的 buffer 读锁，导致写请求事务阻塞在升级锁操作中。在了解完 expensive request 对请求延时的影响后，接下来要如何解决请求延时较高问题的定位效率呢？为了提高请求延时分布的可观测性、延时问题的定位效率，etcd 社区在 3.4 版本后中实现了 trace 特性，详细记录了一个请求在各个阶段的耗时。若某阶段耗时流程超过默认的 100ms，则会打印一条 trace 日志。下面是我将 trace 日志打印的阈值改成 1 纳秒后读请求执行过程中的 trace 日志。从日志中你可以看到，trace 日志记录了以下阶段耗时：

agreement among raft nodes before linearized reading，此阶段读请求向 Leader 发起 readIndex 查询并等待本地 applied index >= Leader 的 committed index， 但是你无法区分是 readIndex 慢还是等待本地 applied index > Leader 的 committed index 慢。在 etcd 3.5 中新增了 trace，区分了以上阶段；get authentication metadata，获取鉴权元数据；range keys from in-memory index tree，从内存索引 B-tree 中查找 key 列表对应的版本号列表；range keys from bolt db，根据版本号列表从 boltdb 遍历，获得用户的 key-value 信息；filter and sort the key-value pairs，过滤、排序 key-value 列表；assemble the response，聚合结果。

```
{
    "level":"info"，
    "ts":"2020-12-16T08:11:43.720+0800"，
    "caller":"traceutil/trace.go:145"，
    "msg":"trace[789864563] range"，
    "detail":"{range_begin:a; range_end:; response_count:1; response_revision:32011; }"，
    "duration":"318.774µs"，
    "start":"2020-12-16T08:11:43.719+0800"，
    "end":"2020-12-16T08:11:43.720+0800"，
    "steps":[
        "trace[789864563] 'agreement among raft nodes before linearized reading'  (duration: 255.227µs)"，
        "trace[789864563] 'get authentication metadata'  (duration: 2.97µs)"，
        "trace[789864563] 'range keys from in-memory index tree'  (duration: 44.578µs)"，
        "trace[789864563] 'range keys from bolt db'  (duration: 8.688µs)"，
        "trace[789864563] 'filter and sort the key-value pairs'  (duration: 578ns)"，
        "trace[789864563] 'assemble the response'  (duration: 643ns)"
    ]
}
```

那么写请求流程会记录哪些阶段耗时呢？下面是 put 写请求的执行 trace 日志，记录了以下阶段耗时：

process raft request，写请求提交到 Raft 模块处理完成耗时；get key's previous created_revision and leaseID，获取 key 上一个创建版本号及 leaseID 的耗时；marshal mvccpb.KeyValue，序列化 KeyValue 结构体耗时；store kv pair into bolt db，存储 kv 数据到 boltdb 的耗时；attach lease to kv pair，将 lease id 关联到 kv 上所用时间。

```
{
    "level":"info"，
    "ts":"2020-12-16T08:25:12.707+0800"，
    "caller":"traceutil/trace.go:145"，
    "msg":"trace[1402827146] put"，
    "detail":"{key:16; req_size:8; response_revision:32030; }"，
    "duration":"6.826438ms"，
    "start":"2020-12-16T08:25:12.700+0800"，
    "end":"2020-12-16T08:25:12.707+0800"，
    "steps":[
        "trace[1402827146] 'process raft request'  (duration: 6.659094ms)"，
        "trace[1402827146] 'get key's previous created_revision and leaseID'  (duration: 23.498µs)"，
        "trace[1402827146] 'marshal mvccpb.KeyValue'  (duration: 1.857µs)"，
        "trace[1402827146] 'store kv pair into bolt db'  (duration: 30.121µs)"，
        "trace[1402827146] 'attach lease to kv pair'  (duration: 661ns)"
    ]
}
```
通过以上介绍的 trace 特性，你就可以快速定位到高延时读写请求的原因。比如当你向 etcd 发起了一个涉及到大量 key 或 value 较大的 expensive request 请求的时候，它会产生如下的 warn 和 trace 日志。从以下日志中我们可以看到，此请求查询的 vip 前缀下所有的 kv 数据总共是 250 条，但是涉及的数据包大小有 250MB，总耗时约 1.85 秒，其中从 boltdb 遍历 key 消耗了 1.63 秒。

```
{
    "level":"warn"，
    "ts":"2020-12-16T23:02:53.324+0800"，
    "caller":"etcdserver/util.go:163"，
    "msg":"apply request took too long"，
    "took":"1.84796759s"，
    "expected-duration":"100ms"，
    "prefix":"read-only range "，
    "request":"key:"vip" range_end:"viq" "，
    "response":"range_response_count:250 size:262150651"
}
{
    "level":"info"，
    "ts":"2020-12-16T23:02:53.324+0800"，
    "caller":"traceutil/trace.go:145"，
    "msg":"trace[370341530] range"，
    "detail":"{range_begin:vip; range_end:viq; response_count:250; response_revision:32666; }"，
    "duration":"1.850335038s"，
    "start":"2020-12-16T23:02:51.473+0800"，
    "end":"2020-12-16T23:02:53.324+0800"，
    "steps":[
        "trace[370341530] 'range keys from bolt db'  (duration: 1.632336981s)"
    ]
}
```
最后，有两个注意事项。第一，在 etcd 3.4 中，logger 默认为 capnslog，trace 特性只有在当 logger 为 zap 时才开启，因此你需要设置 --logger=zap。第二，trace 特性并不能记录所有类型的请求，它目前只覆盖了 MVCC 模块中的 range/put/txn 等常用接口。像 Authenticate 鉴权请求，涉及到大量 CPU 计算，延时是非常高的，在 trace 日志中目前没有相关记录。


如果你开启了密码鉴权，在连接数增多、QPS 增大后，若突然出现请求超时，如何确定是鉴权还是查询、更新等接口导致的呢？etcd 默认参数并不会采集各个接口的延时数据，我们可以通过设置 etcd 的启动参数 --metrics 为 extensive 来开启，获得每个 gRPC 接口的延时数据。同时可结合各个 gRPC 接口的请求数，获得 QPS。如下是某节点的 metrics 数据，251 个 Put 请求，返回码 OK，其中有 240 个请求在 100 毫秒内完成。



```
grpc_server_handled_total{grpc_code="OK"，
grpc_method="Put"，grpc_service="etcdserverpb.KV"，
grpc_type="unary"} 251

grpc_server_handling_seconds_bucket{grpc_method="Put"，grpc_service="etcdserverpb.KV"，grpc_type="unary"，le="0.005"} 0
grpc_server_handling_seconds_bucket{grpc_method="Put"，grpc_service="etcdserverpb.KV"，grpc_type="unary"，le="0.01"} 1
grpc_server_handling_seconds_bucket{grpc_method="Put"，grpc_service="etcdserverpb.KV"，grpc_type="unary"，le="0.025"} 51
grpc_server_handling_seconds_bucket{grpc_method="Put"，grpc_service="etcdserverpb.KV"，grpc_type="unary"，le="0.05"} 204
grpc_server_handling_seconds_bucket{grpc_method="Put"，grpc_service="etcdserverpb.KV"，grpc_type="unary"，le="0.1"} 240

```

### 集群容量、节点 CPU/Memory 瓶颈
介绍完网络、磁盘 I/O、expensive request 导致 etcd 请求延时较高的原因和分析方法后，我们再看看容量和节点资源瓶颈是如何导致高延时请求产生的。若网络、磁盘 I/O 正常，也无 expensive request，那此时高延时请求是怎么产生的呢？它的 trace 日志会输出怎样的耗时结果？下面是一个社区用户反馈的一个读接口高延时案例的两条 trace 日志。从第一条日志中我们可以知道瓶颈在于线性读的准备步骤，readIndex 和 wait applied index。那么是其中具体哪个步骤导致的高延时呢？通过在 etcd 3.5 版本中细化此流程，我们获得了第二条日志，发现瓶颈在于等待 applied index >= Leader 的 committed index。

```
{
"level": "info"，
"ts": "2020-08-12T08:24:56.181Z"，
"caller": "traceutil/trace.go:145"，
"msg": "trace[677217921] range"，
"detail": "{range_begin:/...redacted...; range_end:; response_count:1; response_revision:2725080604; }"，
"duration": "1.553047811s"，
"start": "2020-08-12T08:24:54.628Z"，
"end": "2020-08-12T08:24:56.181Z"，
"steps": [
"trace[677217921] 'agreement among raft nodes before linearized reading'  (duration: 1.534322015s)"
]
}

{
  "level": "info"，
  "ts": "2020-09-22T12:54:01.021Z"，
  "caller": "traceutil/trace.go:152"，
  "msg": "trace[2138445431] linearizableReadLoop"，
  "detail": ""，
  "duration": "855.447896ms"，
  "start": "2020-09-22T12:54:00.166Z"，
  "end": "2020-09-22T12:54:01.021Z"，
  "steps": [
    "trace[2138445431] read index received  (duration: 824.408µs)"，
    "trace[2138445431] applied index is now lower than readState.Index  (duration: 854.622058ms)"
  ]
}
```

为什么会发生这样的现象呢?首先你可以通过 etcd_server_slow_apply_total 指标，观查其值快速增长的时间点与高延时请求产生的日志时间点是否吻合。其次检查是否存在大量写请求。线性读需确保本节点数据与 Leader 数据一样新， 若本节点的数据与 Leader 差异较大，本节点追赶 Leader 数据过程会花费一定时间，最终导致高延时的线性读请求产生。


**etcd 适合读多写少的业务场景，若写请求较大，很容易出现容量瓶颈，导致高延时的读写请求产生**。最后通过 ps/top/mpstat/perf 等 CPU、Memory 性能分析工具，检查 etcd 节点是否存在 CPU、Memory 瓶颈。goroutine 饥饿、内存不足都会导致高延时请求产生，若确定 CPU 和 Memory 存在异常，你可以通过开启 debug 模式，通过 pprof 分析 CPU 和内存瓶颈点。


### 小结
最后小结下我们今天的内容，我按照前面介绍的读写请求原理、以及丰富的实战经验，给你整理了可能导致延时抖动的常见原因。如下图所示，我从以下几个方面给你介绍了会导致请求延时上升的原因：网络质量，如节点之间 RTT 延时、网卡带宽满，出现丢包；磁盘 I/O 抖动，会导致 WAL 日志持久化、boltdb 事务提交出现抖动，Leader 出现切换等；expensive request，比如大包请求、涉及到大量 key 遍历、Authenticate 密码鉴权等操作；容量瓶颈，太多写请求导致线性读请求性能下降等；节点配置，CPU 繁忙导致请求处理延时、内存不够导致 swap 等。

![img](https://static001.geekbang.org/resource/image/93/a3/9375f08cebd596b87b92623c10786fa3.png?wh=1920*1474)

并在分析这些案例的过程中，给你介绍了 etcd 问题核心工具：metrics、etcd log、trace 日志、blktrace、pprof 等。希望通过今天的内容，能帮助你从容应对 etcd 延时抖动。

## 15 | 内存：为什么你的etcd内存占用那么高？

在使用 etcd 的过程中，你是否被异常内存占用等现象困扰过？比如 etcd 中只保存了 1 个 1MB 的 key-value，但是经过若干次修改后，最终 etcd 内存可能达到数 G。它是由什么原因导致的？如何分析呢？这就是我今天要和你分享的主题：etcd 的内存。 希望通过这节课，帮助你掌握 etcd 内存抖动、异常背后的常见原因和分析方法，当你遇到类似问题时，能独立定位、解决。同时，帮助你在实际业务场景中，为集群节点配置充足的内存资源，遵循最佳实践，尽量减少 expensive request，避免 etcd 内存出现突增，导致 OOM。

### 分析整体思路
当你遇到 etcd 内存占用较高的案例时，你脑海中第一反应是什么呢？也许你会立刻重启 etcd 进程，尝试将内存降低到合理水平，避免线上服务出问题。也许你会开启 etcd debug 模式，重启 etcd 进程等复现，然后采集 heap profile 分析内存占用。以上措施都有其合理性。但作为团队内 etcd 高手的你，在集群稳定性还不影响业务的前提下，能否先通过内存异常的现场，结合 etcd 的读写流程、各核心模块中可能会使用较多内存的关键数据结构，推测出内存异常的可能原因？全方位的分析内存异常现场，可以帮助我们节省大量复现和定位时间，也是你专业性的体现。下图是我以 etcd 写请求流程为例，给你总结的可能导致 etcd 内存占用较高的核心模块与其数据结构。

![img](https://static001.geekbang.org/resource/image/c2/49/c2673ebb2db4b555a9fbe229ed1bda49.png?wh=1920*1056)

从图中你可以看到，当 etcd 收到一个写请求后，gRPC Server 会和你建立连接。连接数越多，会导致 etcd 进程的 fd、goroutine 等资源上涨，因此会使用越来越多的内存。其次，基于我们04介绍的 Raft 知识背景，它需要将此请求的日志条目保存在 raftLog 里面。etcd raftLog 后端实现是内存存储，核心就是数组。因此 raftLog 使用的内存与其保存的日志条目成正比，它也是内存分析过程中最容易被忽视的一个数据结构。然后当此日志条目被集群多数节点确认后，在应用到状态机的过程中，会在内存 treeIndex 模块的 B-tree 中创建、更新 key 与版本号信息。 在这过程中 treeIndex 模块的 B-tree 使用的内存与 key、历史版本号数量成正比。更新完 treeIndex 模块的索引信息后，etcd 将 key-value 数据持久化存储到 boltdb。boltdb 使用了 mmap 技术，将 db 文件映射到操作系统内存中。因此在未触发操作系统将 db 对应的内存 page 换出的情况下，etcd 的 db 文件越大，使用的内存也就越大。同时，在这个过程中还有两个注意事项。



一方面，其他 client 可能会创建若干 watcher、监听这个写请求涉及的 key， etcd 也需要使用一定的内存维护 watcher、推送 key 变化监听的事件。另一方面，如果这个写请求的 key 还关联了 Lease，Lease 模块会在内存中使用数据结构 Heap 来快速淘汰过期的 Lease，因此 Heap 也是一个占用一定内存的数据结构。最后，不仅仅是写请求流程会占用内存，读请求本身也会导致内存上升。尤其是 expensive request，当产生大包查询时，MVCC 模块需要使用内存保存查询的结果，很容易导致内存突增。基于以上读写流程图对核心数据结构使用内存的分析，我们定位问题时就有线索、方法可循了。那如何确定是哪个模块、场景导致的内存异常呢？接下来我就通过一个实际案例，和你深入介绍下内存异常的分析方法。

### 一个 key 使用数 G 内存的案例
我们通过 goreman 启动一个 3 节点 etcd 集群 (linux/etcd v3.4.9)，db quota 为 6G，执行如下的命令并观察 etcd 内存占用情况：执行 1000 次的 put 同一个 key 操作，value 为 1MB；更新完后并进行 compact、defrag 操作；

```
# put同一个key，执行1000次
for i in {1..1000}; do dd if=/dev/urandom bs=1024 
count=1024  | ETCDCTL_API=3 etcdctl put key  || break; done

# 获取最新revision，并压缩
etcdctl compact `(etcdctl endpoint status --write-out="json" | egrep -o '"revision":[0-9]*' | egrep -o '[0-9].*')`

# 对集群所有节点进行碎片整理
etcdctl defrag --cluster
```

在执行操作前，空集群 etcd db size 20KB，etcd 进程内存 36M 左右，分别如下图所示。

![img](https://static001.geekbang.org/resource/image/c1/e6/c1fb89ae1d6218a66cf1db30c41d9be6.png?wh=1164*358)



![img](https://static001.geekbang.org/resource/image/6c/6d/6ce074583f39cd9a19bdcb392133426d.png?wh=1318*420)



你预测执行 1000 次同样 key 更新后，etcd 进程占用了多少内存呢? 约 37M？ 1G？ 2G？3G？ 还是其他呢？执行 1000 次的 put 操作后，db 大小和 etcd 内存占用分别如下图所示。

![img](https://static001.geekbang.org/resource/image/d6/45/d6dc86f76f52dfed73ab1771ebbbf545.png?wh=1302*348)

![img](https://static001.geekbang.org/resource/image/9d/70/9d97762851c18a0c4cd89aa5a7bb0270.png?wh=1310*404)

当我们执行 compact、defrag 命令后，如下图所示，db 大小只有 1M 左右，但是你会发现 etcd 进程实际却仍占用了 2G 左右内存。

![img](https://static001.geekbang.org/resource/image/93/bd/937c3fb0bf12595928e8ae4b05b7a5bd.png?wh=1260*354)

![img](https://static001.geekbang.org/resource/image/8d/58/8d2d9fb3c0193745d80fe68b0cb4a758.png?wh=1276*406)

整个集群只有一个 key，为什么 etcd 占用了这么多的内存呢？是 etcd 发生了内存泄露吗？

### raftLog
当你发起一个 put 请求的时候，etcd 需通过 Raft 模块将此请求同步到其他节点，详细流程你可结合下图再次了解下。

![img](https://static001.geekbang.org/resource/image/df/2c/df9yy18a1e28e18295cfc15a28cd342c.png?wh=1920*1328)

从图中你可以看到，Raft 模块的输入是一个消息 /Msg，输出统一为 Ready 结构。etcd 会把此请求封装成一个消息，提交到 Raft 模块。Raft 模块收到此请求后，会把此消息追加到 raftLog 的 unstable 存储的 entry 内存数组中（图中流程 2），并且将待持久化的此消息封装到 Ready 结构内，通过管道通知到 etcdserver（图中流程 3）。etcdserver 取出消息，持久化到 WAL 中，并追加到 raftLog 的内存存储 storage 的 entry 数组中（图中流程 5）。下面是[raftLog](https://github.com/etcd-io/etcd/blob/v3.4.9/raft/log.go#L24:L45)的核心数据结构，它由 storage、unstable、committed、applied 等组成。storage 存储已经持久化到 WAL 中的日志条目，unstable 存储未持久化的条目和快照，一旦持久化会及时删除日志条目，因此不存在过多内存占用的问题。

```
type raftLog struct {
   // storage contains all stable entries since the last snapshot.
   storage Storage


   // unstable contains all unstable entries and snapshot.
   // they will be saved into storage.
   unstable unstable


   // committed is the highest log position that is known to be in
   // stable storage on a quorum of nodes.
   committed uint64
   // applied is the highest log position that the application has
   // been instructed to apply to its state machine.
   // Invariant: applied <= committed
   applied uint64
}
```
从上面 raftLog 结构体中，你可以看到，存储稳定的日志条目的 storage 类型是 Storage，Storage 定义了存储 Raft 日志条目的核心 API 接口，业务应用层可根据实际场景进行定制化实现。etcd 使用的是 Raft 算法库本身提供的 MemoryStorage，其定义如下，核心是使用了一个数组来存储已经持久化后的日志条目。

```
// MemoryStorage implements the Storage interface backed
// by an in-memory array.
type MemoryStorage struct {
   // Protects access to all fields. Most methods of MemoryStorage are
   // run on the raft goroutine， but Append() is run on an application
   // goroutine.
   sync.Mutex

   hardState pb.HardState
   snapshot  pb.Snapshot
   // ents[i] has raftLog position i+snapshot.Metadata.Index
   ents []pb.Entry
}
```
那么随着写请求增多，内存中保留的 Raft 日志条目会越来越多，如何防止 etcd 出现 OOM 呢？etcd 提供了快照和压缩功能来解决这个问题。首先你可以通过调整 --snapshot-count 参数来控制生成快照的频率，其值默认是 100000（etcd v3.4.9，早期 etcd 版本是 10000），也就是每 10 万个写请求触发一次快照生成操作。快照生成完之后，etcd 会通过压缩来删除旧的日志条目。


那么是全部删除日志条目还是保留一小部分呢？答案是保留一小部分 Raft 日志条目。数量由 DefaultSnapshotCatchUpEntries 参数控制，默认 5000，目前不支持自定义配置。保留一小部分日志条目其实是为了帮助慢的 Follower 以较低的开销向 Leader 获取 Raft 日志条目，以尽快追上 Leader 进度。若 raftLog 中不保留任何日志条目，就只能发送快照给慢的 Follower，这开销就非常大了。通过以上分析可知，如果你的请求 key-value 比较大，比如上面我们的案例中是 1M，1000 次修改，那么 etcd raftLog 至少会消耗 1G 的内存。这就是为什么内存随着写请求修改次数不断增长的原因。除了 raftLog 占用内存外，MVCC 模块的 treeIndex/boltdb 模块又是如何使用内存的呢？

### treeIndex
一个 put 写请求的日志条目被集群多数节点确认提交后，这时 etcdserver 就会从 Raft 模块获取已提交的日志条目，应用到 MVCC 模块的 treeIndex 和 boltdb。我们知道 treeIndex 是基于 google 内存 btree 库实现的一个索引管理模块，在 etcd 中每个 key 都会在 treeIndex 中保存一个索引项 (keyIndex)，记录你的 key 和版本号等信息，如下面的数据结构所示。

```
type keyIndex struct {
   key         []byte
   modified    revision // the main rev of the last modification
   generations []generation
}
```
同时，你每次对 key 的修改、删除操作都会在 key 的索引项中追加一条修改记录 (revision)。因此，随着修改次数的增加，etcd 内存会一直增加。那么如何清理旧版本，防止过多的内存占用呢？答案也是压缩。正如我在11压缩篇和你介绍的，当你执行 compact 命令时，etcd 会遍历 treeIndex 中的各个 keyIndex，清理历史版本号记录与已删除的 key，释放内存。从上面的 keyIndex 数据结构我们可知，一个 key 的索引项内存开销跟你的 key 大小、保存的历史版本数、compact 策略有关。为了避免内存索引项占用过多的内存，key 的长度不应过长，同时你需要配置好合理的压缩策略。

### boltdb
在 treeIndex 模块中创建、更新完 keyIndex 数据结构后，你的 key-value 数据、各种版本号、lease 等相关信息会保存到如下的一个 mvccpb.keyValue 结构体中。它是 boltdb 的 value，key 则是 treeIndex 中保存的版本号，然后通过 boltdb 的写接口保存到 db 文件中。

```
kv := mvccpb.KeyValue{
   Key:            key，
   Value:          value，
   CreateRevision: c，
   ModRevision:    rev，
   Version:        ver，
   Lease:          int64(leaseID)，
}
```
前面我们在介绍 boltdb 时，提到过 etcd 在启动时会通过 mmap 机制，将 etcd db 文件映射到 etcd 进程地址空间，并设置 mmap 的 MAP_POPULATE flag，它会告诉 Linux 内核预读文件，让 Linux 内核将文件内容拷贝到物理内存中。在节点内存足够的情况下，后续读请求可直接从内存中获取。相比 read 系统调用，mmap 少了一次从 page cache 拷贝到进程内存地址空间的操作，因此具备更好的性能。若 etcd 节点内存不足，可能会导致 db 文件对应的内存页被换出。当读请求命中的页未在内存中时，就会产生缺页异常，导致读过程中产生磁盘 IO。这样虽然避免了 etcd 进程 OOM，但是此过程会产生较大的延时。从以上 boltdb 的 key-value 和 mmap 机制介绍中我们可知，我们应控制 boltdb 文件大小，优化 key-value 大小，配置合理的压缩策略，回收旧版本，避免过多内存占用。



### watcher
在你写入 key 的时候，其他 client 还可通过 etcd 的 Watch 监听机制，获取到 key 的变化事件。那创建一个 watcher 耗费的内存跟哪些因素有关呢?在08Watch 机制设计与实现分析中，我和你介绍过创建 watcher 的整体流程与架构，如下图所示。当你创建一个 watcher 时，client 与 server 建立连接后，会创建一个 gRPC Watch Stream，随后通过这个 gRPC Watch Stream 发送创建 watcher 请求。每个 gRPC Watch Stream 中 etcd WatchServer 会分配两个 goroutine 处理，一个是 sendLoop，它负责 Watch 事件的推送。一个是 recvLoop，负责接收 client 的创建、取消 watcher 请求消息。同时对每个 watcher 来说，etcd 的 WatchableKV 模块需将其保存到相应的内存管理数据结构中，实现可靠的 Watch 事件推送。

![img](https://static001.geekbang.org/resource/image/42/bf/42575d8d0a034e823b8e48d4ca0a49bf.png?wh=1920*1075)

因此 watch 监听机制耗费的内存跟 client 连接数、gRPC Stream、watcher 数 (watching) 有关，如下面公式所示：c1 表示每个连接耗费的内存；c2 表示每个 gRPC Stream 耗费的内存；c3 表示每个 watcher 耗费的内存。


```
memory = c1 * number_of_conn + c2 * 
avg_number_of_stream_per_conn + c3 * 
avg_number_of_watch_stream
```

根据 etcd 社区的[压测报告](https://etcd.io/docs/v3.4.0/benchmarks/etcd-3-watch-memory-benchmark/)，大概估算出 Watch 机制中 c1、c2、c3 占用的内存分别如下：每个 client 连接消耗大约 17kb 的内存 (c1)；每个 gRPC Stream 消耗大约 18kb 的内存 (c2)；每个 watcher 消耗大约 350 个字节 (c3)；

当你的业务场景大量使用 watcher 的时候，应提前估算下内存容量大小，选择合适的内存配置节点。注意以上估算并不包括 watch 事件堆积的开销。变更事件较多，服务端、客户端高负载，网络阻塞等情况都可能导致事件堆积。在 etcd 3.4.9 版本中，每个 watcher 默认 buffer 是 1024。buffer 内保存 watch 响应结果，如 watchID、watch 事件（watch 事件包含 key、value）等。若大量事件堆积，将产生较高昂的内存的开销。你可以通过 etcd_debugging_mvcc_pending_events_total 指标监控堆积的事件数，etcd_debugging_slow_watcher_total 指标监控慢的 watcher 数，来及时发现异常。


### expensive request
当你写入比较大的 key-value 后，如果 client 频繁查询它，也会产生高昂的内存开销。假设我们写入了 100 个这样 1M 大小的 key， 通过 Range 接口一次查询 100 个 key， 那么 boltdb 遍历、反序列化过程将花费至少 100MB 的内存。如下面代码所示，它会遍历整个 key-value，将 key-value 保存到数组 kvs 中。

```
kvs := make([]mvccpb.KeyValue， limit)
revBytes := newRevBytes()
for i， revpair := range revpairs[:len(kvs)] {
   revToBytes(revpair， revBytes)
   _， vs := tr.tx.UnsafeRange(keyBucketName， revBytes， nil， 0)
   if len(vs) != 1 {
        ......    
   }
   if err := kvs[i].Unmarshal(vs[0]); err != nil {
        .......
   }
```
也就是说，一次查询就耗费了至少 100MB 的内存、产生了至少 100MB 的流量，随着你 QPS 增大后，很容易 OOM、网卡出现丢包。count-only、limit 查询在 key 百万级以上时，也会产生非常大的内存开销。因为它们在遍历 treeIndex 的过程中，会将相关 key 保存在数组里面。当 key 多时，此开销不容忽视。正如我在13  db 大小中讲到的，在 master 分支，我已提交相关 PR 解决 count-only 和 limit 查询导致内存占用突增的问题。



### etcd v2/goroutines/bug
除了以上介绍的核心模块、expensive request 场景可能导致较高的内存开销外，还有以下场景也会导致 etcd 内存使用较高。


首先是 etcd 中使用了 v2 的 API 写入了大量的 key-value 数据，这会导致内存飙高。我们知道 etcd v2 的 key-value 都是存储在内存树中的，同时 v2 的 watcher 不支持多路复用，内存开销相比 v3 多了一个数量级。在 etcd 3.4 版本之前，etcd 默认同时支持 etcd v2/v3 API，etcd 3.4 版本默认关闭了 v2 API。 你可以通过 etcd v2 API 和 etcd v2 内存存储模块的 metrics 前缀 etcd_debugging_store，观察集群中是否有 v2 数据导致的内存占用高。其次是 goroutines 泄露导致内存占用高。此问题可能会在容器化场景中遇到。etcd 在打印日志的时候，若出现阻塞则可能会导致 goroutine 阻塞并持续泄露，最终导致内存泄露。你可以通过观察、监控 go_goroutines 来发现这个问题。最后是 etcd bug 导致的内存泄露。当你基本排除以上场景导致的内存占用高后，则很可能是 etcd bug 导致的内存泄露。


比如早期 etcd clientv3 的 lease keepalive 租约频繁续期 bug，它会导致 Leader 高负载、内存泄露，此 bug 已在 3.2.24/3.3.9 版本中修复。还有最近我修复的 etcd 3.4 版本的Follower 节点[内存泄露](https://github.com/etcd-io/etcd/pull/11731)。具体表现是两个 Follower 节点内存一直升高，Leader 节点正常，已在 3.4.6 版本中修复。若内存泄露并不是已知的 etcd bug 导致，那你可以开启 pprof， 尝试复现，通过分析 pprof heap 文件来确定消耗大量内存的模块和数据结构。


### 小节
今天我通过一个写入 1MB  key 的实际案例，给你介绍了可能导致 etcd 内存占用高的核心数据结构、场景，同时我将可能导致内存占用较高的因素总结为了下面这幅图，你可以参考一下。

![img](https://static001.geekbang.org/resource/image/aa/90/aaf7b4f5f6f568dc70c1a0964fb92790.png?wh=1920*684)

首先是 raftLog。为了帮助 slow Follower 同步数据，它至少要保留 5000 条最近收到的写请求在内存中。若你的 key 非常大，你更新 5000 次会产生较大的内存开销。其次是 treeIndex。 每个 key-value 会在内存中保留一个索引项。索引项的开销跟 key 长度、保留的历史版本有关，你可以通过 compact 命令压缩。然后是 boltdb。etcd 启动的时候，会通过 mmap 系统调用，将文件映射到虚拟内存中。你可以通过 compact 命令回收旧版本，defrag 命令进行碎片整理。接着是 watcher。它的内存占用跟连接数、gRPC Watch Stream 数、watcher 数有关。watch 机制一个不可忽视的内存开销其实是事件堆积的占用缓存，你可以通过相关 metrics 及时发现堆积的事件以及 slow watcher。最后我介绍了一些典型的场景导致的内存异常，如大包查询等 expensive request，etcd 中存储了 v2 API 写入的 key， goroutines 泄露以及 etcd lease bug 等。希望今天的内容，能够帮助你从容应对 etcd 内存占用高的问题，合理配置你的集群，优化业务 expensive request，让 etcd 跑得更稳。


## 16 | 性能及稳定性（上）：如何优化及扩展etcd性能？

在使用 etcd 的过程中，你是否吐槽过 etcd 性能差呢？ 我们知道，etcd 社区线性读[压测结果](https://etcd.io/docs/v3.4.0/op-guide/performance/)可以达到 14w/s，那为什么在实际业务场景中有时却只有几千，甚至几百、几十，还会偶发超时、频繁抖动呢？我相信不少人都遇到过类似的问题。要解决这些问题，不仅需要了解症结所在，还需要掌握优化和扩展 etcd 性能的方法，对症下药。因为这部分内容比较多，所以我分成了两讲内容，分别从读性能、写性能和稳定性入手，为你详细讲解如何优化及扩展 etcd 性能及稳定性。希望通过这两节课的学习，能让你在使用 etcd 的时候，设计出良好的业务存储结构，遵循最佳实践，让 etcd 稳定、高效地运行，获得符合预期的性能。同时，当你面对 etcd 性能瓶颈的时候，也能自己分析瓶颈原因、选择合适的优化方案解决它，而不是盲目甩锅 etcd，甚至更换技术方案去 etcd 化。

今天这节课，我将重点为你介绍如何提升读的性能。我们说读性能差，其实本质是读请求链路中某些环节出现了瓶颈。所以，接下来我将通过一张读性能分析链路图，为你从上至下分析影响 etcd 性能、稳定性的若干因素，并给出相应的压测数据，最终为你总结出一系列的 etcd 性能优化和扩展方法。


### 性能分析链路
为什么在你的业务场景中读性能不如预期呢？ 是读流程中的哪一个环节出现了瓶颈？在下图中，我为你总结了一个开启密码鉴权场景的读性能瓶颈分析链路图，并在每个核心步骤数字旁边，标出了影响性能的关键因素。我之所以选用密码鉴权的读请求为案例，是因为它使用较广泛并且请求链路覆盖最全，同时它也是最容易遇到性能瓶颈的场景。

![img](https://static001.geekbang.org/resource/image/7f/52/7f8c66ded3e151123b18768b880a2152.png?wh=1920*1253)


接下来我将按照这张链路分析图，带你深入分析一个使用密码鉴权的线性读请求，和你一起看看影响它性能表现的核心因素以及最佳优化实践。


### 负载均衡
首先是流程一负载均衡。在02 节时我和你提到过，在 etcd 3.4 以前，client 为了节省与 server 节点的连接数，clientv3 负载均衡器最终只会选择一个 sever 节点 IP，与其建立一个长连接。但是这可能会导致对应的 server 节点过载（如单节点流量过大，出现丢包）， 其他节点却是低负载，最终导致业务无法获得集群的最佳性能。在 etcd 3.4 后，引入了 Round-robin 负载均衡算法，它通过轮询的方式依次从 endpoint 列表中选择一个 endpoint 访问 (长连接)，使 server 节点负载尽量均衡。所以，如果你使用的是 etcd 低版本，那么我建议你通过 Load Balancer 访问后端 etcd 集群。因为一方面 Load Balancer 一般支持配置各种负载均衡算法，如连接数、Round-robin 等，可以使你的集群负载更加均衡，规避 etcd client 早期的固定连接缺陷，获得集群最佳性能。另一方面，当你集群节点需要替换、扩缩容集群节点的时候，你不需要去调整各个 client 访问 server 的节点配置。

### 选择合适的鉴权
client 通过负载均衡算法为请求选择好 etcd server 节点后，client 就可调用 server 的 Range RPC 方法，把请求发送给 etcd server。在此过程中，如果 server 启用了鉴权，那么就会返回无权限相关错误给 client。如果 server 使用的是密码鉴权，你在创建 client 时，需指定用户名和密码。etcd clientv3 库发现用户名、密码非空，就会先校验用户名和密码是否正确。client 是如何向 sever 请求校验用户名、密码正确性的呢？client 是通过向 server 发送 Authenticate RPC 鉴权请求实现密码认证的，也就是图中的流程二。

![img](https://static001.geekbang.org/resource/image/9e/61/9e1fb86567b351641db9586081c0e361.png?wh=1920*1267)



根据我们05介绍的密码认证原理，server 节点收到鉴权请求后，它会从 boltdb 获取此用户密码对应的算法版本、salt、cost 值，并基于用户的请求明文密码计算出一个 hash 值。在得到 hash 值后，就可以对比 db 里保存的 hash 密码是否与其一致了。如果一致，就会返回一个 token 给 client。 这个 token 是 client 访问 server 节点的通行证，后续 server 只需要校验“通行证”是否有效即可，无需每次发起昂贵的 Authenticate RPC 请求。讲到这里，不知道你有没有意识到，若你的业务在访问 etcd 过程中未复用 token，每次访问 etcd 都发起一次 Authenticate 调用，这将是一个非常大的性能瓶颈和隐患。因为正如我们 05 所介绍的，为了保证密码的安全性，密码认证（Authenticate）的开销非常昂贵，涉及到大量 CPU 资源。那这个 Authenticate 接口究竟有多慢呢？

为了得到 Authenticate 接口的性能，我们做过这样一个测试：压测集群 etcd 节点配置是 16 核 32G；压测方式是我们通过修改 etcd clientv3 库、benchmark 工具，使 benchmark 工具支持 Authenticate 接口压测；然后设置不同的 client 和 connection 参数，运行多次，观察结果是否稳定，获取测试结果。

最终的测试结果非常惊人。etcd v3.4.9 之前的版本，Authenticate 接口性能不到 16 QPS，并且随着 client 和 connection 增多，该性能会继续恶化。当 client 和 connection 的数量达到 200 个的时候，性能会下降到 8 QPS，P99 延时为 18 秒，如下图所示。

![img](https://static001.geekbang.org/resource/image/bc/c4/bc6336b93de53e6650bd7a5565ef8ec4.png?wh=1470*1284)


对此，我和小伙伴王超凡通过一个[减少锁的范围 PR](https://github.com/etcd-io/etcd/pull/11735)（该 PR 已经 cherry-pick 到了 etcd 3.4.9 版本），将性能优化到了约 200 QPS，并且 P99 延时在 1 秒内，如下图所示。由于导致 Authenticate 接口性能差的核心瓶颈，是在于密码鉴权使用了 bcrpt 计算 hash 值，因此 Authenticate 性能已接近极限。

![img](https://static001.geekbang.org/resource/image/44/aa/449bb47bef89a7cf1d2fbb1205a15faa.png?wh=1286*1362)

最令人头疼的是，Auenticate 的调用由 clientv3 库默默发起的，etcd 中也没有任何日志记录其耗时等。当大家开启密码鉴权后，遇到读写接口超时的时候，未详细了解 etcd 的同学就会非常困惑，很难定位超时本质原因。我曾多次收到小伙伴的求助，协助他们排查 etcd 异常超时问题。通过 metrics 定位，我发现这些问题大都是由比较频繁的 Authenticate 调用导致，只要临时关闭鉴权或升级到 etcd v3.4.9 版本就可以恢复。为了帮助大家快速发现 Authenticate 等特殊类型的 expensive request，我在 etcd 3.5 版本中提交了一个 PR，通过 gRPC 拦截器的机制，当一个请求超过 300ms 时，就会打印整个请求信息。讲到这里，你应该会有疑问，密码鉴权的性能如此差，可是业务又需要使用它，我们该怎么解决密码鉴权的性能问题呢？对此，我有三点建议。

第一，如果你的生产环境需要开启鉴权，并且读写 QPS 较大，那我建议你不要图省事使用密码鉴权。最好使用证书鉴权，这样能完美避坑认证性能差、token 过期等问题，性能几乎无损失。第二，确保你的业务每次发起请求时有复用 token 机制，尽可能减少 Authenticate RPC 调用。第三，如果你使用密码鉴权时遇到性能瓶颈问题，可将 etcd 升级到 3.4.9 及以上版本，能适当提升密码鉴权的性能。


### 选择合适的读模式
client 通过 server 的鉴权后，就可以发起读请求调用了，也就是我们图中的流程三。

![img](https://static001.geekbang.org/resource/image/58/9a/5832f5da0f916b941b1d832e9fe2e29a.png?wh=1920*1270)

在这个步骤中，读模式对性能有着至关重要的影响。我们前面讲过 etcd 提供了串行读和线性读两种读模式。前者因为不经过 ReadIndex 模块，具有低延时、高吞吐量的特点；而后者在牺牲一点延时和吞吐量的基础上，实现了数据的强一致性读。这两种读模式分别为不同场景的读提供了解决方案。关于串行读和线性读的性能对比，下图我给出了一个测试结果，测试环境如下：

机器配置 client 16 核 32G，三个 server 节点 8 核 16G、SSD 盘，client 与 server 节点都在同可用区；各节点之间 RTT 在 0.1ms 到 0.2ms 之间；etcd v3.4.9 版本；1000 个 client。

执行如下串行读压测命令：


```
benchmark --endpoints=addr --conns=100 --clients=1000 \
range hello --consistency=s --total=500000
```

得到串行读压测结果如下，32 万 QPS，平均延时 2.5ms。

![img](https://static001.geekbang.org/resource/image/3d/9a/3d18aafb016a93e8d2f07a4193cb6b9a.png?wh=1426*1336)

执行如下线性读压测命令：


```
benchmark --endpoints=addr --conns=100 --clients=1000 \
range hello --consistency=l --total=500000
```
得到线性读压测结果如下，19 万 QPS，平均延时 4.9ms。

![img](https://static001.geekbang.org/resource/image/83/0d/831338d142bc44999cc6c3b04147yy0d.png?wh=1474*1346)

从两个压测结果图中你可以看到，在 100 个连接时，串行读性能比线性读性能高近 11 万 /s，串行读请求延时（2.5ms）比线性读延时约低一半（4.9ms）。

需要注意的是，以上读性能数据是在 1 个 key、没有任何写请求、同可用区的场景下压测出来的，实际的读性能会随着你的写请求增多而出现显著下降，这也是实际业务场景性能与社区压测结果存在非常大差距的原因之一。所以，我建议你使用 etcd benchmark 工具在你的 etcd 集群环境中自测一下，你也可以参考下面的etcd 社区[压测结果](https://etcd.io/docs/v3.4.0/op-guide/performance/)。

![img](https://static001.geekbang.org/resource/image/58/ca/58135ebf14a25e3f74004929369867ca.png?wh=1398*590)

如果你的业务场景读 QPS 较大，但是你又不想通过 etcd proxy 等机制来扩展性能，那你可以进一步评估业务场景对数据一致性的要求高不高。如果你可以容忍短暂的不一致，那你可以通过串行读来提升 etcd 的读性能，也可以部署 Learner 节点给可能会产生 expensive read request 的业务使用，实现 cheap/expensive read request 隔离。

### 线性读实现机制、网络延时
了解完读模式对性能的影响后，我们继续往下分析。在我们这个密码鉴权读请求的性能分析案例中，读请求使用的是 etcd 默认线性读模式。线性读对应图中的流程四、流程五，其中流程四对应的是 ReadIndex，流程五对应的是等待本节点数据追上 Leader 的进度（ApplyWait）。

![img](https://static001.geekbang.org/resource/image/f0/f1/f018b98629360e7c6eef6f9cfb0241f1.png?wh=1920*1162)

在早期的 etcd 3.0 版本中，etcd 线性读是基于 Raft log read 实现的。每次读请求要像写请求一样，生成一个 Raft 日志条目，然后提交给 Raft 一致性模块处理，基于 Raft 日志执行的有序性来实现线性读。因为该过程需要经过磁盘 I/O，所以性能较差。为了解决 Raft log read 的线性读性能瓶颈，etcd 3.1 中引入了 ReadIndex。ReadIndex 仅涉及到各个节点之间网络通信，因此节点之间的 RTT 延时对其性能有较大影响。虽然同可用区可获取到最佳性能，但是存在单可用区故障风险。如果你想实现高可用区容灾的话，那就必须牺牲一点性能了。跨可用区部署时，各个可用区之间延时一般在 2 毫秒内。如果跨城部署，服务性能就会下降较大。所以一般场景下我不建议你跨城部署，你可以通过 Learner 节点实现异地容灾。如果异地的服务对数据一致性要求不高，那么你甚至可以通过串行读访问 Learner 节点，来实现就近访问，低延时。各个节点之间的 RTT 延时，是决定流程四 ReadIndex 性能的核心因素之一。


### 磁盘 IO 性能、写 QPS
到了流程五，影响性能的核心因素就是磁盘 IO 延时和写 QPS。

![img](https://static001.geekbang.org/resource/image/73/bc/732ec57338e1yy1d932e959ed776c0bc.png?wh=1920*1336)

如下面代码所示，流程五是指节点从 Leader 获取到最新已提交的日志条目索引 (rs.Index) 后，它需要等待本节点当前已应用的 Raft 日志索引，大于等于 Leader 的已提交索引，确保能在本节点状态机中读取到最新数据。


```
if ai := s.getAppliedIndex(); ai < rs.Index {
   select {
   case <-s.applyWait.Wait(rs.Index):
   case <-s.stopping:
      return
   }
}
// unblock all l-reads requested at indices before rs.Index
nr.notify(nil)
```
而应用已提交日志条目到状态机的过程中又涉及到随机写磁盘，详情可参考我们03中介绍过 etcd 的写请求原理。因此我们可以知道，**etcd 是一个对磁盘 IO 性能非常敏感的存储系统，磁盘 IO 性能不仅会影响 Leader 稳定性、写性能表现，还会影响读性能。线性读性能会随着写性能的增加而快速下降。如果业务对性能、稳定性有较大要求，我建议你尽量使用 SSD 盘。**
下表我给出了一个 8 核 16G 的三节点集群，在总 key 数只有一个的情况下，随着写请求增大，线性读性能下降的趋势总结（基于 benchmark 工具压测结果），你可以直观感受下读性能是如何随着写性能下降。

![img](https://static001.geekbang.org/resource/image/40/5a/4069e72370942764ef4905715267c05a.jpg?wh=1475*620)

当本节点已应用日志条目索引大于等于 Leader 已提交的日志条目索引后，读请求就会接到通知，就可通过 MVCC 模块获取数据。


### RBAC 规则数、Auth 锁
读请求到了 MVCC 模块后，首先要通过鉴权模块判断此用户是否有权限访问请求的数据路径，也就是流程六。影响流程六的性能因素是你的 RBAC 规则数和锁。首先是 RBAC 规则数，为了解决快速判断用户对指定 key 范围是否有权限，etcd 为每个用户维护了读写权限区间树。基于区间树判断用户访问的范围是否在用户的读写权限区间内，时间复杂度仅需要 O(logN)。

另外一个因素则是 AuthStore 的锁。在 etcd 3.4.9 之前的，校验密码接口可能会占用较长时间的锁，导致授权接口阻塞。etcd 3.4.9 之后合入了缩小锁范围的 PR，可一定程度降低授权接口被阻塞的问题。

### expensive request、treeIndex 锁
通过流程六的授权后，则进入流程七，从 treeIndex 中获取整个查询涉及的 key 列表版本号信息。在这个流程中，影响其性能的关键因素是 treeIndex 的总 key 数、查询的 key 数、获取 treeIndex 锁的耗时。

![img](https://static001.geekbang.org/resource/image/9d/da/9dfe22355a9fd841943fb1c4556db9da.png?wh=1920*1272)

首先，treeIndex 中总 key 数过多会适当增大我们遍历的耗时。其次，若要访问 treeIndex 我们必须获取到锁，但是可能其他请求如 compact 操作也会获取锁。早期的时候，它需要遍历所有索引，然后进行数据压缩工作。这就会导致其他请求阻塞，进而增大延时。为了解决这个性能问题，优化方案是 compact 的时候会将 treeIndex 克隆一份，以空间来换时间，尽量降低锁阻塞带来的超时问题。

接下来我重点给你介绍下查询 key 数较多等 expensive read request 时对性能的影响。假设我们链路分析图中的请求是查询一个 Kubernetes 集群所有 Pod，当你 Pod 数一百以内的时候可能对 etcd 影响不大，但是当你 Pod 数千甚至上万的时候， 流程七、八就会遍历大量的 key，导致请求耗时突增、内存上涨、性能急剧下降。你可结合13db 大小、14延时、15内存三节一起看看，这里我就不再重复描述。如果业务就是有这种 expensive read request 逻辑，我们该如何应对呢？

首先我们可以尽量减少 expensive read request 次数，在程序启动的时候，只 List 一次全量数据，然后通过 etcd Watch 机制去获取增量变更数据。比如 Kubernetes 的 Informer 机制，就是典型的优化实践。其次，在设计上评估是否能进行一些数据分片、拆分等，不同场景使用不同的 etcd prefix 前缀。比如在 Kubernetes 中，不要把 Pod 全部都部署在 default 命名空间下，尽量根据业务场景按命名空间拆分部署。即便每个场景全量拉取，也只需要遍历自己命名空间下的资源，数据量上将下降一个数量级。再次，如果你觉得 Watch 改造大、数据也无法分片，开发麻烦，你可以通过分页机制按批拉取，尽量减少一次性拉取数万条数据。最后，如果以上方式都不起作用的话，你还可以通过引入 cache 实现缓存 expensive read request 的结果，不过应用需维护缓存数据与 etcd 的一致性。



### 大 key-value、boltdb 锁
从流程七获取到 key 列表及版本号信息后，我们就可以访问 boltdb 模块，获取 key-value 信息了。在这个流程中，影响其性能表现的，除了我们上面介绍的 expensive read request，还有大 key-value 和锁。首先是大 key-value。我们知道 etcd 设计上定位是个小型的元数据存储，它没有数据分片机制，默认 db quota 只有 2G，实践中往往不会超过 8G，并且针对每个 key-value 大小，它也进行了大小限制，默认是 1.5MB。大 key-value 非常容易导致 etcd OOM、server 节点出现丢包、性能急剧下降等。那么当我们往 etcd 集群写入一个 1MB 的 key-value 时，它的线性读性能会从 17 万 QPS 具体下降到多少呢?我们可以执行如下 benchmark 命令：

```
benchmark --endpoints=addr --conns=100 --clients=1000 \
range key --consistency=l --total=10000
```

得到其结果如下，从下图你可以看到，读取一个 1MB 的 key-value，线性读性能 QPS 下降到 1163，平均延时上升到 818ms，可见大 key-value 对性能的巨大影响。

![img](https://static001.geekbang.org/resource/image/a0/c7/a0735af4c2efd4156d392f75yyf132c7.png?wh=1296*1324)

同时，从下面的 etcd 监控图上你也可以看到内存出现了突增，若存在大量大 key-value 时，可想而知，etcd 内存肯定暴涨，大概率会 OOM。

![img](https://static001.geekbang.org/resource/image/95/78/9599ec869c1496e8f9a8e5e54acb5b78.png?wh=1326*412)



其次是锁，etcd 为了提升 boltdb 读的性能，从 etcd 3.1 到 etcd 3.4 版本，分别进行过几次重大优化，在下一节中我将和你介绍。以上就是一个开启密码鉴权场景，线性读请求的性能瓶颈分析过程。


### 小结
今天我通过从上至下的请求流程分析，介绍了各个流程中可能存在的瓶颈和优化方法、最佳实践等。优化读性能的核心思路是首先我们可通过 etcd clientv3 自带的 Round-robin 负载均衡算法或者 Load Balancer，尽量确保整个集群负载均衡。然后，在开启鉴权场景时，建议你尽量使用证书而不是密码认证，避免校验密码的昂贵开销。其次，根据业务场景选择合适的读模式，串行读比线性度性能提高 30% 以上，延时降低一倍。线性读性能受节点之间 RTT 延时、磁盘 IO 延时、当前写 QPS 等多重因素影响。最容易被大家忽视的就是写 QPS 对读 QPS 的影响，我通过一系列压测数据，整理成一个表格，让你更直观感受写 QPS 对读性能的影响。多可用区部署会导致节点 RTT 延时增高，读性能下降。因此你需要在高可用和高性能上做取舍和平衡。最后在访问数据前，你的读性能还可能会受授权性能、expensive read request、treeIndex 及 boltdb 的锁等影响。你需要遵循最佳实践，避免一个请求查询大量 key、大 key-value 等，否则会导致读性能剧烈下降。希望你通过本文当遇到读 etcd 性能问题时，能从请求执行链路去分析瓶颈，解决问题，让业务和 etcd 跑得更稳、更快。



## 17 | 性能及稳定性（下）：如何优化及扩展etcd性能?
我们继续来看如何优化及扩展 etcd 性能。上一节课里我为你重点讲述了如何提升读的性能，今天我将重点为你介绍如何提升写性能和稳定性，以及如何基于 etcd gRPC Proxy 扩展 etcd 性能。当你使用 etcd 写入大量 key-value 数据的时候，是否遇到过 etcd server 返回"etcdserver: too many requests"错误？这个错误是怎么产生的呢？我们又该如何来优化写性能呢？这节课我将通过写性能分析链路图，为你从上至下分析影响写性能、稳定性的若干因素，并为你总结出若干 etcd 写性能优化和扩展方法。

### 性能分析链路
为什么你写入大量 key-value 数据的时候，会遇到 Too Many Request 限速错误呢？ 是写流程中的哪些环节出现了瓶颈？和读请求类似，我为你总结了一个开启鉴权场景的写性能瓶颈及稳定性分析链路图，并在每个核心步骤数字旁边标识了影响性能、稳定性的关键因素。

![img](https://static001.geekbang.org/resource/image/14/0a/14ac1e7f1936f2def67b7fa24914070a.png?wh=1920*1167)

下面我将按照这个写请求链路分析图，和你深入分析影响 etcd 写性能的核心因素和最佳优化实践。

### db quota
首先是流程一。在 etcd v3.4.9 版本中，client 会通过 clientv3 库的 Round-robin 负载均衡算法，从 endpoint 列表中轮询选择一个 endpoint 访问，发起 gRPC 调用。然后进入流程二。etcd 收到 gRPC 写请求后，首先经过的是 Quota 模块，它会影响写请求的稳定性，若 db 大小超过配额就无法写入。

![img](https://static001.geekbang.org/resource/image/89/e8/89c9ccbf210861836cc3b5929b7ebae8.png?wh=1920*1227)



etcd 是个小型的元数据存储，默认 db quota 大小是 2G，超过 2G 就只读无法写入。因此你需要根据你的业务场景，适当调整 db quota 大小，并配置的合适的压缩策略。正如我在11里和你介绍的，etcd 支持按时间周期性压缩、按版本号压缩两种策略，建议压缩策略不要配置得过于频繁。比如如果按时间周期压缩，一般情况下 5 分钟以上压缩一次比较合适，因为压缩过程中会加一系列锁和删除 boltdb 数据，过于频繁的压缩会对性能有一定的影响。一般情况下 db 大小尽量不要超过 8G，过大的 db 文件和数据量对集群稳定性各方面都会有一定的影响，详细你可以参考13。


### 限速
通过流程二的 Quota 模块后，请求就进入流程三 KVServer 模块。在 KVServer 模块里，影响写性能的核心因素是限速。

![img](https://static001.geekbang.org/resource/image/78/14/78062ff5b8c5863d8802bdfacf32yy14.png?wh=1920*1233)

KVServer 模块的写请求在提交到 Raft 模块前，会进行限速判断。如果 Raft 模块已提交的日志索引（committed index）比已应用到状态机的日志索引（applied index）超过了 5000，那么它就返回一个"etcdserver: too many requests"错误给 client。那么哪些情况可能会导致 committed Index 远大于 applied index 呢?首先是 long expensive read request 导致写阻塞。比如 etcd 3.4 版本之前长读事务会持有较长时间的 buffer 读锁，而写事务又需要升级锁更新 buffer，因此出现写阻塞乃至超时。最终导致 etcd server 应用已提交的 Raft 日志命令到状态机缓慢。堆积过多时，则会触发限速。其次 etcd 定时批量将 boltdb 写事务提交的时候，需要对 B+ tree 进行重平衡、分裂，并将 freelist、dirty page、meta page 持久化到磁盘。此过程需要持有 boltdb 事务锁，若磁盘随机写性能较差、瞬间大量写入，则也容易写阻塞，应用已提交的日志条目缓慢。最后执行 defrag 等运维操作时，也会导致写阻塞，它们会持有相关锁，导致写性能下降。


### 心跳及选举参数优化
写请求经过 KVServer 模块后，则会提交到流程四的 Raft 模块。我们知道 etcd 写请求需要转发给 Leader 处理，因此影响此模块性能和稳定性的核心因素之一是集群 Leader 的稳定性。

![img](https://static001.geekbang.org/resource/image/66/2c/660a03c960cd56610e3c43e15c14182c.png?wh=1920*1247)



那如何判断 Leader 的稳定性呢?答案是日志和 metrics。一方面，在你使用 etcd 过程中，你很可能见过如下 Leader 发送心跳超时的警告日志，你可以通过此日志判断集群是否有频繁切换 Leader 的风险。另一方面，你可以通过 etcd_server_leader_changes_seen_total metrics 来观察已发生 Leader 切换的次数。

```
21:30:27 etcd3 | {"level":"warn","ts":"2021-02-23T21:30:27.255+0800","caller":"wal/wal.go:782","msg":"slow fdatasync","took":"3.259857956s","expected-duration":"1s"}
21:30:30 etcd3 | {"level":"warn","ts":"2021-02-23T21:30:30.396+0800","caller":"etcdserver/raft.go:390","msg":"leader failed to send out heartbeat on time; took too long, leader is overloaded likely from slow disk","to":"91bc3c398fb3c146","heartbeat-interval":"100ms","expected-duration":"200ms","exceeded-duration":"827.162111ms"}
```
那么哪些因素会导致此日志产生以及发生 Leader 切换呢?首先，我们知道 etcd 是基于 Raft 协议实现数据复制和高可用的，各节点会选出一个 Leader，然后 Leader 将写请求同步给各个 Follower 节点。而 Follower 节点如何感知 Leader 异常，发起选举，正是依赖 Leader 的心跳机制。在 etcd 中，Leader 节点会根据 heartbeart-interval 参数（默认 100ms）定时向 Follower 节点发送心跳。如果两次发送心跳间隔超过 2*heartbeart-interval，就会打印此警告日志。超过 election timeout（默认 1000ms），Follower 节点就会发起新一轮的 Leader 选举。

哪些原因会导致心跳超时呢？一方面可能是你的磁盘 IO 比较慢。因为 etcd 从 Raft 的 Ready 结构获取到相关待提交日志条目后，它需要将此消息写入到 WAL 日志中持久化。你可以通过观察 etcd_wal_fsync_durations_seconds_bucket 指标来确定写 WAL 日志的延时。若延时较大，你可以使用 SSD 硬盘解决。另一方面也可能是 CPU 使用率过高和网络延时过大导致。CPU 使用率较高可能导致发送心跳的 goroutine 出现饥饿。若 etcd 集群跨地域部署，节点之间 RTT 延时大，也可能会导致此问题。

最后我们应该如何调整心跳相关参数，以避免频繁 Leader 选举呢？etcd 默认心跳间隔是 100ms，较小的心跳间隔会导致发送频繁的消息，消耗 CPU 和网络资源。而较大的心跳间隔，又会导致检测到 Leader 故障不可用耗时过长，影响业务可用性。一般情况下，为了避免频繁 Leader 切换，建议你可以根据实际部署环境、业务场景，将心跳间隔时间调整到 100ms 到 400ms 左右，选举超时时间要求至少是心跳间隔的 10 倍。

### 网络和磁盘 IO 延时
当集群 Leader 稳定后，就可以进入 Raft 日志同步流程。我们假设收到写请求的节点就是 Leader，写请求通过 Propose 接口提交到 Raft 模块后，Raft 模块会输出一系列消息。etcd server 的 raftNode goroutine 通过 Raft 模块的输出接口 Ready，获取到待发送给 Follower 的日志条目追加消息和待持久化的日志条目。raftNode goroutine 首先通过 HTTP 协议将日志条目追加消息广播给各个 Follower 节点，也就是流程五。

![img](https://static001.geekbang.org/resource/image/8d/eb/8dd9d414eb4ef3ba9a7603fayy991aeb.png?wh=1920*1254)

流程五涉及到各个节点之间网络通信，因此节点之间 RTT 延时对其性能有较大影响。跨可用区、跨地域部署时性能会出现一定程度下降，建议你结合实际网络环境使用 benchmark 工具测试一下。etcd Raft 网络模块在实现上，也会通过流式发送和 pipeline 等技术优化来降低延时、提高网络性能。同时，raftNode goroutine 也会将待持久化的日志条目追加到 WAL 中，它可以防止进程 crash 后数据丢失，也就是流程六。注意此过程需要同步等待数据落地，因此磁盘顺序写性能决定着性能优异。为了提升写吞吐量，etcd 会将一批日志条目批量持久化到磁盘。etcd 是个对磁盘 IO 延时非常敏感的服务，如果服务对性能、稳定性有较大要求，建议你使用 SSD 盘。那使用 SSD 盘的 etcd 集群和非 SSD 盘的 etcd 集群写性能差异有多大呢？下面是 SSD 盘集群，执行如下 benchmark 命令的压测结果，写 QPS 51298，平均延时 189ms。

```
benchmark --endpoints=addr --conns=100 --clients=1000 \
    put --key-size=8 --sequential-keys --total=10000000 --
val-size=256
```

![img](https://static001.geekbang.org/resource/image/91/14/913e9875ef32df415426a3e5e7cff814.png?wh=1424*1364)

下面是非 SSD 盘集群，执行同样 benchmark 命令的压测结果，写 QPS 35255，平均延时 279ms。

![img](https://static001.geekbang.org/resource/image/17/2f/1758a57804be463228e6431a388c552f.png?wh=1432*1354)


### 快照参数优化
在 Raft 模块中，正常情况下，Leader 可快速地将我们的 key-value 写请求同步给其他 Follower 节点。但是某 Follower 节点若数据落后太多，Leader 内存中的 Raft 日志已经被 compact 了，那么 Leader 只能发送一个快照给 Follower 节点重建恢复。在快照较大的时候，发送快照可能会消耗大量的 CPU、Memory、网络资源，那么它就会影响我们的读写性能，也就是我们图中的流程七。

![img](https://static001.geekbang.org/resource/image/1a/38/1ab7a084e61d84f44b893a0fbbdc0138.png?wh=1920*1262)

一方面， etcd Raft 模块引入了流控机制，来解决日志同步过程中可能出现的大量资源开销、导致集群不稳定的问题。另一方面，我们可以通过快照参数优化，去降低 Follower 节点通过 Leader 快照重建的概率，使其尽量能通过增量的日志同步保持集群的一致性。etcd 提供了一个名为 --snapshot-count 的参数来控制快照行为。它是指收到多少个写请求后就触发生成一次快照，并对 Raft 日志条目进行压缩。为了帮助 slower Follower 赶上 Leader 进度，etcd 在生成快照，压缩日志条目的时候也会至少保留 5000 条日志条目在内存中。那 snapshot-count 参数设置多少合适呢?

snapshot-count 值过大它会消耗较多内存，你可以参考 15 内存篇中 Raft 日志内存占用分析。过小则的话在某节点数据落后时，如果它请求同步的日志条目 Leader 已经压缩了，此时我们就不得不将整个 db 文件发送给落后节点，然后进行快照重建。快照重建是极其昂贵的操作，对服务质量有较大影响，因此我们需要尽量避免快照重建。etcd 3.2 版本之前 snapshot-count 参数值是 1 万，比较低，短时间内大量写入就较容易触发慢的 Follower 节点快照重建流程。etcd 3.2 版本后将其默认值调大到 10 万，老版本升级的时候，你需要注意配置文件是否写死固定的参数值。



### 大 value
当写请求对应的日志条目被集群多数节点确认后，就可以提交到状态机执行了。etcd 的 raftNode goroutine 就可通过 Raft 模块的输出接口 Ready，获取到已提交的日志条目，然后提交到 Apply 模块的 FIFO 待执行队列。因为它是串行应用执行命令，任意请求在应用到状态机时阻塞都会导致写性能下降。当 Raft 日志条目命令从 FIFO 队列取出执行后，它会首先通过授权模块校验是否有权限执行对应的写操作，对应图中的流程八。影响其性能因素是 RBAC 规则数和锁。

![img](https://static001.geekbang.org/resource/image/53/f6/5303f1b003480d2ddfe7dbd56b05b3f6.png?wh=1920*1179)



然后通过权限检查后，写事务则会从 treeIndex 模块中查找 key、更新的 key 版本号等信息，对应图中的流程九，影响其性能因素是 key 数和锁。更新完索引后，我们就可以把新版本号作为 boltdb key， 把用户 key/value、版本号等信息组合成一个 value，写入到 boltdb，对应图中的流程十，影响其性能因素是大 value、锁。如果你在应用中保存 1Mb 的 value，这会给 etcd 稳定性带来哪些风险呢？首先会导致读性能大幅下降、内存突增、网络带宽资源出现瓶颈等，上节课我已和你分享过一个 1MB 的 key-value 读性能压测结果，QPS 从 17 万骤降到 1100 多。那么写性能具体会下降到多少呢？通过 benchmark 执行如下命令写入 1MB 的数据时候，集群几乎不可用（三节点 8 核 16G，非 SSD 盘），事务提交 P99 延时高达 4 秒，如下图所示。

```
benchmark --endpoints=addr --conns=100 --clients=1000 \
put --key-size=8 --sequential-keys --total=500 --val-
size=1024000
```

![img](https://static001.geekbang.org/resource/image/0c/bb/0c2635d617245f5d4084fbe48820e4bb.png?wh=1007*157)

因此只能将写入的 key-value 大小调整为 100KB。执行后得到如下结果，写入 QPS 仅为 1119/S，平均延时高达 324ms。

![img](https://static001.geekbang.org/resource/image/a7/63/a745af37d76208c08be147ac46018463.png?wh=1302*1324)



其次 etcd 底层使用的 boltdb 存储，它是个基于 COW(Copy-on-write) 机制实现的嵌入式 key-value 数据库。较大的 value 频繁更新，因为 boltdb 的 COW 机制，会导致 boltdb 大小不断膨胀，很容易超过默认 db quota 值，导致无法写入。那如何优化呢？

首先，如果业务已经使用了大 key，拆分、改造存在一定客观的困难，那我们就从问题的根源之一的写入对症下药，尽量不要频繁更新大 key，这样 etcd db 大小就不会快速膨胀。你可以从业务场景考虑，判断频繁的更新是否合理，能否做到增量更新。之前遇到一个 case， 一个业务定时更新大量 key，导致被限速，最后业务通过增量更新解决了问题。如果写请求降低不了， 就必须进行精简、拆分你的数据结构了。将你需要频繁更新的数据拆分成小 key 进行更新等，实现将 value 值控制在合理范围以内，才能让你的集群跑的更稳、更高效。Kubernetes 的 Node 心跳机制优化就是这块一个非常优秀的实践。早期 kubelet 会每隔 10s 上报心跳更新 Node 资源。但是此资源对象较大，导致 db 大小不断膨胀，无法支撑更大规模的集群。为了解决这个问题，社区做了数据拆分，将经常变更的数据拆分成非常细粒度的对象，实现了集群稳定性提升，支撑住更大规模的 Kubernetes 集群。


### boltdb 锁
了解完大 value 对集群性能的影响后，我们再看影响流程十的另外一个核心因素 boltdb 锁。首先我们回顾下 etcd 读写性能优化历史，它经历了以下流程：

3.0 基于 Raft log read 实现线性读，线性读需要经过磁盘 IO，性能较差；3.1 基于 ReadIndex 实现线性读，每个节点只需要向 Leader 发送 ReadIndex 请求，不涉及磁盘 IO，提升了线性读性能；3.2 将访问 boltdb 的锁从互斥锁优化到读写锁，提升了并发读的性能；3.4 实现全并发读，去掉了 buffer 锁，长尾读几乎不再影响写。

并发读特性的核心原理是创建读事务对象时，它会全量拷贝当前写事务未提交的 buffer 数据，并发的读写事务不再阻塞在一个 buffer 资源锁上，实现了全并发读。最重要的是，写事务也不再因为 expensive read request 长时间阻塞，有效的降低了写请求的延时，详细测试结果你可以参考**并发读特性实现 PR**，因篇幅关系就不再详细描述。


### 扩展性能
当然有不少业务场景你即便用最高配的硬件配置，etcd 可能还是无法解决你所面临的性能问题。etcd 社区也考虑到此问题，提供了一个名为gRPC proxy的组件，帮助你扩展读、扩展 watch、扩展 Lease 性能的机制，如下图所示。

![img](https://static001.geekbang.org/resource/image/4a/b1/4a13ec9a4f93931e6e0656c600c2d3b1.png?wh=1920*1042)


### 扩展读
如果你的 client 比较多，etcd 集群节点连接数大于 2 万，或者你想平行扩展串行读的性能，那么 gRPC proxy 就是良好一个解决方案。它是个无状态节点，为你提供高性能的读缓存的能力。你可以根据业务场景需要水平扩容若干节点，同时通过连接复用，降低服务端连接数、负载。它也提供了故障探测和自动切换能力，当后端 etcd 某节点失效后，会自动切换到其他正常节点，业务 client 可对此无感知。


### 扩展 Watch
大量的 watcher 会显著增大 etcd server 的负载，导致读写性能下降。etcd 为了解决这个问题，gRPC proxy 组件里面提供了 watcher 合并的能力。如果多个 client Watch 同 key 或者范围（如上图三个 client Watch 同 key）时，它会尝试将你的 watcher 进行合并，降低服务端的 watcher 数。然后当它收到 etcd 变更消息时，会根据每个 client 实际 Watch 的版本号，将增量的数据变更版本，分发给你的多个 client，实现 watch 性能扩展及提升。


### 扩展 Lease
我们知道 etcd Lease 特性，提供了一种客户端活性检测机制。为了确保你的 key 不被淘汰，client 需要定时发送 keepalive 心跳给 server。当 Lease 非常多时，这就会导致 etcd 服务端的负载增加。在这种场景下，gRPC proxy 提供了 keepalive 心跳连接合并的机制，来降低服务端负载。


### 小结
今天我通过从上至下的写请求流程分析，介绍了各个流程中可能存在的瓶颈和优化方法、最佳实践。最后我从分层的角度，为你总结了一幅优化思路全景图，你可以参考一下下面这张图，它将我们这两节课讨论的 etcd 性能优化、扩展问题分为了以下几类：

业务应用层，etcd 应用层的最佳实践；etcd 内核层，etcd 参数最佳实践；操作系统层，操作系统优化事项；硬件及网络层，不同的硬件设备对 etcd 性能有着非常大的影响；扩展性能，基于 gRPC proxy 扩展读、Watch、Lease 的性能。

希望你通过这节课的学习，以后在遇到 etcd 性能问题时，能分别从请求执行链路和分层的视角去分析、优化瓶颈，让业务和 etcd 跑得更稳、更快。

![img](https://static001.geekbang.org/resource/image/92/87/928a4f1e66200531f5ee73aab000ce87.png?wh=1920*1091)

## 18 | 实战：如何基于Raft从0到1构建一个支持多存储引擎分布式KV服务？

通过前面课程的学习，我相信你已经对 etcd 基本架构、核心特性有了一定理解。如果让你基于 Raft 协议，实现一个简易的类 etcd、支持多存储引擎的分布式 KV 服务，并能满足读多写少、读少写多的不同业务场景诉求，你知道该怎么动手吗？纸上得来终觉浅，绝知此事要躬行。今天我就和你聊聊如何实现一个类 etcd、支持多存储引擎的 KV 服务，我们将基于 etcd 自带的[raftexample](https://github.com/etcd-io/etcd/tree/v3.4.9/contrib/raftexample)项目快速构建它。为了方便后面描述，我把它命名为 metcd（表示微型的 etcd），它是 raftexample 的加强版。希望通过 metcd 这个小小的实战项目，能够帮助你进一步理解 etcd 乃至分布式存储服务的核心架构、原理、典型问题解决方案。同时在这个过程中，我将详细为你介绍 etcd 的 Raft 算法工程实现库、不同类型存储引擎的优缺点，拓宽你的知识视野，为你独立分析 etcd 源码，夯实基础。

### 整体架构设计
在和你深入聊代码细节之前，首先我和你从整体上介绍下系统架构。下面是我给你画的 metcd 整体架构设计，它由 API 层、Raft 层的共识模块、逻辑层及存储层组成的状态机组成。接下来，我分别和你简要分析下 API 设计及复制状态机。

![img](https://static001.geekbang.org/resource/image/5e/03/5e9f6882a6f6e357e5c2c5yyffda4e03.png?wh=1920*1166)


### API 设计
API 是软件系统对外的语言，它是应用编程接口的缩写，由一组接口定义和协议组成。在设计 API 的时候，我们往往会考虑以下几个因素：

性能。如 etcd v2 使用的是简单的 HTTP/1.x，性能上无法满足大规模 Kubernetes 集群等场景的诉求，因此 etcd v3 使用的是基于 HTTP/2 的 gRPC 协议。易用性、可调试性。如有的内部高并发服务为了满足性能等诉求，使用的是 UDP 协议。相比 HTTP 协议，UDP 协议显然在易用性、可调试性上存在一定的差距。开发效率、跨平台、可移植性。相比基于裸 UDP、TCP 协议设计的接口，如果你使用 Protobuf 等 IDL 语言，它支持跨平台、代码自动自动生成，开发效率更高。安全性。如相比 HTTP 协议，使用 HTTPS 协议可对通信数据加密更安全，可适用于不安全的网络环境（比如公网传输）。接口幂等性。幂等性简单来说，就是同样一个接口请求一次与多次的效果一样。若你的接口对外保证幂等性，则可降低使用者的复杂度。

因为我们场景的是 POC(Proof of concept)、Demo 开发，因此在 metcd 项目中，我们优先考虑点是易用性、可调试性，选择 HTTP/1.x 协议，接口上为了满足 key-value 操作，支持 Get 和 Put 接口即可。假设 metcd 项目使用 3379 端口，Put 和 Get 接口，如下所示。

Put 接口，设置 key-value
```
curl -L http://127.0.0.1:3379/hello -XPUT -d world
```
Get 接口，查询 key-value
```
curl -L http://127.0.0.1:3379/hello
world
```

### 复制状态机
了解完 API 设计，那最核心的复制状态机是如何工作的呢？我们知道 etcd 是基于下图复制状态机实现的分布式 KV 服务，复制状态机由共识模块、日志模块、状态机组成。

![img](https://static001.geekbang.org/resource/image/5c/4f/5c7a3079032f90120a6b309ee401fc4f.png?wh=605*319)

我们的实战项目 metcd，也正是使用与之一样的模型，并且使用 etcd 项目中实现的 Raft 算法库作为共识模块，此算法库已被广泛应用在 etcd、cockroachdb、dgraph 等开源项目中。以下是复制状态机的写请求流程：

client 发起一个写请求（put hello = world）；server 向 Raft 共识模块提交请求，共识模块生成一个写提案日志条目。若 server 是 Leader，则把日志条目广播给其他节点，并持久化日志条目到 WAL 中；当一半以上节点持久化日志条目后，Leader 的共识模块将此日志条目标记为已提交（committed），并通知其他节点提交；server 从共识模块获取已经提交的日志条目，异步应用到状态机存储中（boltdb/leveldb/memory），然后返回给 client。

### 多存储引擎
了解完复制状态机模型后，我和你再深入介绍下状态机。状态机中最核心模块当然是存储引擎，那要如何同时支持多种存储引擎呢？metcd 项目将基于 etcd 本身自带的 raftexample 项目进行快速开发，而 raftexample 本身只支持内存存储。因此我们通过将 KV 存储接口进行抽象化设计，实现支持多存储引擎。KVStore interface 的定义如下所示。

```
type KVStore interface {
   // LookUp get key value
   Lookup(key string) (string, bool)

   // Propose propose kv request into raft state machine
   Propose(k, v string)

   // ReadCommits consume entry from raft state machine into KvStore map until error
   ReadCommits(commitC <-chan *string, errorC <-chan error)

   // Snapshot return KvStore snapshot
   Snapshot() ([]byte, error)

   // RecoverFromSnapshot recover data from snapshot
   RecoverFromSnapshot(snapshot []byte) error

   // Close close backend databases
   Close() err
}
```
基于 KV 接口抽象化的设计，我们只需要针对具体的存储引擎，实现对应的操作即可。我们期望支持三种存储引擎，分别是内存 map、boltdb、leveldb，并做一系列简化设计。一组 metcd 实例，通过 metcd 启动时的配置来决定使用哪种存储引擎。不同业务场景不同实例，比如读多写少的存储引擎可使用 boltdb，写多读少的可使用 leveldb。接下来我和你重点介绍下存储引擎的选型及原理。

### boltdb
boltdb 是一个基于 B+ tree 实现的存储引擎库，在10中我已和你详细介绍过原理。boltdb 为什么适合读多写少？对于读请求而言，一般情况下它可直接从内存中基于 B+ tree 遍历，快速获取数据返回给 client，不涉及经过磁盘 I/O。对于写请求，它基于 B+ tree 查找写入位置，更新 key-value。事务提交时，写请求包括 B+ tree 重平衡、分裂、持久化 ditry page、持久化 freelist、持久化 meta page 流程。同时，ditry page 可能分布在文件的各个位置，它发起的是随机写磁盘 I/O。因此在 boltdb 中，完成一个写请求的开销相比读请求是大很多的。正如我在16和17中给你介绍的一样，一个 3 节点的 8 核 16G 空集群，线性读性能可以达到 19 万 QPS，而写 QPS 仅为 5 万。


### leveldb
那要如何设计适合写多读少的存储引擎呢?最简单的思路当然是写内存最快。可是内存有限的，无法支撑大容量的数据存储，不持久化数据会丢失。那能否直接将数据顺序追加到文件末尾（AOF）呢？因为磁盘的特点是顺序写性能比较快。当然可以。[Bitcask](https://en.wikipedia.org/wiki/Bitcask)存储模型就是采用 AOF 模式，把写请求顺序追加到文件。Facebook 的图片存储[Haystack](https://www.usenix.org/legacy/event/osdi10/tech/full_papers/Beaver.pdf)根据其论文介绍，也是使用类似的方案来解决大规模写入痛点。那在 AOF 写入模型中如何实现查询数据呢？


很显然通过遍历文件一个个匹配 key 是可以的，但是它的性能是极差的。为了实现高性能的查询，最理想的解决方案从直接从内存中查询，但是内存是有限的，那么我们能否通过内存索引来记录一个 key-value 数据在文件中的偏移量，实现从磁盘快速读取呢？是的，这正是Bitcask存储模型的查询的实现，它通过内存哈希表维护各个 key-value 数据的索引，实现了快速查找 key-value 数据。不过，内存中虽然只保存 key 索引信息，但是当 key 较多的时候，其对内存要求依然比较高。快速了解完存储引擎提升写性能的核心思路（随机写转化为顺序写）之后，那 leveldb 它的原理是怎样的呢？与 Bitcask 存储模型有什么不一样？leveldb 是基于 LSM tree(log-structured merge-tree) 实现的 key-value 存储，它的架构如下图所示（引用自[微软博客](https://microsoft.github.io/MLOS/notebooks/LevelDbTuning/)）。它提升写性能的核心思路同样是将随机写转化为顺序写磁盘 WAL 文件和内存，结合了我们上面讨论的写内存和磁盘两种方法。数据持久化到 WAL 文件是为了确保机器 crash 后数据不丢失。

![img](https://static001.geekbang.org/resource/image/05/50/05f01951fe5862a62624b81e2ceea150.png?wh=992*677)

那么它要如何解决内存不足和查询的痛点问题呢？核心解决方案是分层的设计和基于一系列对象的转换和压缩。接下来我给你分析一下上面架构图写流程和后台 compaction 任务：

首先写请求顺序写入 Log 文件 (WAL)；更新内存的 Memtable。leveldb Memtable 后端数据结构实现是 skiplist，skiplist 相比平衡二叉树，实现简单却同样拥有高性能的读写；当 Memtable 达到一定的阈值时，转换成不可变的 Memtable，也就是只读不可写；leveldb 后台 Compact 任务会将不可变的 Memtable 生成 SSTable 文件，它有序地存储一系列 key-value 数据。注意 SST 文件按写入时间进行了分层，Level 层次越小数据越新。Manifest 文件记录了各个 SSTable 文件处于哪个层级、它的最小与最大 key 范围；当某个 level 下的 SSTable 文件数目超过一定阈值后，Compact 任务会从这个 level 的 SSTable 中选择一个文件（level>0），将其和高一层级的 level+1 的 SSTable 文件合并；注意 level 0 是由 Immutable 直接生成的，因此 level 0 SSTable 文件中的 key-value 存在相互重叠。而 level > 0 时，在和更高一层 SSTable 合并过程中，参与的 SSTable 文件是多个，leveldb 会确保各个 SSTable 中的 key-value 不重叠。

了解完写流程，读流程也就简单了，核心步骤如下：从 Memtable 跳跃表中查询 key；未找到则从 Immutable 中查找；Immutable 仍未命中，则按照 leveldb 的分层属性，因 level 0 SSTable 文件是直接从 Immutable 生成的，level 0 存在特殊性，因此你需要从 level 0 遍历 SSTable 查找 key；level 0 中若未命中，则从 level 1 乃至更高的层次查找。level 大于 0 时，各个 SSTable 中的 key 是不存在相互重叠的。根据 manifest 记录的 key-value 范围信息，可快递定位到具体的 SSTable。同时 leveldb 基于bloom filter实现了快速筛选 SSTable，因此查询效率较高。

更详细原理你可以参考一下[leveldb](https://github.com/google/leveldb)源码。

### 实现分析
从 API 设计、复制状态机、多存储引擎支持等几个方面你介绍了 metcd 架构设计后，接下来我就和你重点介绍下共识模块、状态机支持多存储引擎模块的核心实现要点。


### Raft 算法库
共识模块使用的是 etcd [Raft 算法库](https://github.com/etcd-io/etcd/tree/v3.4.9/raft)，它是一个经过大量业务生产环境检验、具备良好可扩展性的共识算法库。它提供了哪些接口给你使用? 如何提交一个提案，并且获取 Raft 共识模块输出结果呢？

### Raft API
Raft 作为一个库，它对外最核心的对象是一个名为Node的数据结构。Node 表示 Raft 集群中的一个节点，它的输入与输出接口如下图所示，下面我重点和你介绍它的几个接口功能：

Campaign，状态转换成 Candidate，发起新一轮 Leader 选举；Propose，提交提案接口；Ready，Raft 状态机输出接口，它的返回是一个输出 Ready 数据结构类型的管道，应用需要监听此管道，获取 Ready 数据，处理其中的各个消息（如持久化未提交的日志条目到 WAL 中，发送消息给其他节点等）；Advance，通知 Raft 状态机，应用已处理上一个输出的 Ready 数据，等待发送下一个 Ready 数据；TransferLeaderShip，尝试将 Leader 转移到某个节点；Step，向 Raft 状态机提交收到的消息，比如当 Leader 广播完 MsgApp 消息给 Follower 节点后，Leader 收到 Follower 节点回复的 MsgAppResp 消息时，就通过 Step 接口将此消息提交给 Raft 状态机驱动其工作；ReadIndex，用于实现线性读。



![img](https://static001.geekbang.org/resource/image/a7/39/a79a97f8cc8294dcb93f9552fb638f39.png?wh=1920*787)


上面提到的 Raft 状态机的输出[Ready 结构](https://github.com/etcd-io/etcd/blob/v3.4.9/raft/node.go#L52:L90)含有哪些信息呢? 下图是其详细字段，含义如下：SoftState，软状态。包括集群 Leader 和节点状态，不需要持久化到 WAL；pb.HardState，硬状态。与软状态相反，包括了节点当前 Term、Vote 等信息，需要持久化到 WAL 中；ReadStates，用于线性一致性读；Entries，在向其他节点发送消息之前需持久化到 WAL 中；Messages，持久化 Entries 后，发送给其他节点的消息；Committed Entries，已提交的日志条目，需要应用到存储状态机中；Snapshot，快照需保存到持久化存储中；MustSync，HardState 和 Entries 是否要持久化到 WAL 中；

![img](https://static001.geekbang.org/resource/image/c0/d6/c0f0b8046a7c8c67c277fed9548251d6.png?wh=1920*786)

了解完 API 后，我们接下来继续看看代码如何使用 Raft 的 Node API。正如我在04中和你介绍的，etcd Raft 库的设计抽象了网络、Raft 日志存储等模块，它本身并不会进行网络、存储相关的操作，上层应用需结合自己业务场景选择内置的模块或自定义实现网络、存储、日志等模块。因此我们在使用 Raft 库时，需要先自定义好相关网络、存储等模块，再结合上面介绍的 Raft Node API，就可以完成一个 Node 的核心操作了。其数据结构定义如下：

```
// A key-value stream backed by raft
type raftNode struct {
   proposeC    <-chan string            // proposed messages (k,v)
   confChangeC <-chan raftpb.ConfChange // proposed cluster config changes
   commitC     chan<- *string           // entries committed to log (k,v)
   errorC      chan<- error             // errors from raft session
   id          int      // client ID for raft session
   ......
   node        raft.Node
   raftStorage *raft.MemoryStorage
   wal         *wal.WAL
   transport *rafthttp.Transport
}

```
这个数据结构名字叫 raftNode，它表示 Raft 集群中的一个节点。它是由我们业务应用层设计的一个组合结构。从结构体定义中你可以看到它包含了 Raft 核心数据结构 Node(raft.Node)、Raft 日志条目内存存储模块 (raft.MemoryStorage）、WAL 持久化模块 (wal.WAL) 以及网络模块 (rafthttp.Transport)。同时，它提供了三个核心的管道与业务逻辑模块、存储状态机交互：


proposeC，它用来接收 client 发送的写请求提案消息；confChangeC，它用来接收集群配置变化消息；commitC，它用来输出 Raft 共识模块已提交的日志条目消息。

在 metcd 项目中因为我们是直接基于 raftexample 定制开发，因此日志持久化存储、网络都使用的是 etcd 自带的 WAL 和 rafthttp 模块。[WAL](https://github.com/etcd-io/etcd/blob/v3.4.9/wal/wal.go)模块中提供了核心的保存未持久化的日志条目和快照功能接口，你可以参考03节写请求中我和你介绍的原理。[rafthttp](https://github.com/etcd-io/etcd/tree/v3.4.9/etcdserver/api/rafthttp)模块基于 HTTP 协议提供了各个节点间的消息发送能力，metcd 使用如下：


```
rc.transport = &rafthttp.Transport{
   Logger:      zap.NewExample(),
   ID:          types.ID(rc.id),
   ClusterID:   0x1000,
   Raft:        rc,
   ServerStats: stats.NewServerStats("", ""),
   LeaderStats: stats.NewLeaderStats(strconv.Itoa(rc.id)),
   ErrorC:      make(chan error),
}
```

搞清楚 Raft 模块的输入、输出 API，设计好 raftNode 结构，复用 etcd 的 WAL、网络等模块后，接下来我们就只需要实现如下两个循环逻辑，处理业务层发送给 proposeC 和 confChangeC 消息、将 Raft 的 Node 输出 Ready 结构进行相对应的处理即可。精简后的代码如下所示：


```
func (rc *raftNode) serveChannels() {
   // send proposals over raft
   go func() {
      confChangeCount := uint64(0)
      for rc.proposeC != nil && rc.confChangeC != nil {
         select {
         case prop, ok := <-rc.proposeC:
            if !ok {
               rc.proposeC = nil
            } else {
               // blocks until accepted by raft state machine
               rc.node.Propose(context.TODO(), []byte(prop))
            }

         case cc, ok := <-rc.confChangeC:
            if !ok {
               rc.confChangeC = nil
            } else {
               confChangeCount++
               cc.ID = confChangeCount
               rc.node.ProposeConfChange(context.TODO(), cc)
            }
         }
      }
   }()

   // event loop on raft state machine updates
   for {
      select {
      case <-ticker.C:
         rc.node.Tick()

      // store raft entries to wal, then publish over commit channel
      case rd := <-rc.node.Ready():
         rc.wal.Save(rd.HardState, rd.Entries)
         if !raft.IsEmptySnap(rd.Snapshot) {
            rc.saveSnap(rd.Snapshot)
            rc.raftStorage.ApplySnapshot(rd.Snapshot)
            rc.publishSnapshot(rd.Snapshot)
         }
         rc.raftStorage.Append(rd.Entries)
         rc.transport.Send(rd.Messages)
         if ok := rc.publishEntries(rc.entriesToApply(rd.CommittedEntries)); !ok {
            rc.stop()
            return
         }
         rc.maybeTriggerSnapshot()
         rc.node.Advance()
      }
   }
}
```
代码简要分析如下：从 proposeC 中取出提案消息，通过 raft.Node.Propose API 提交提案；从 confChangeC 取出配置变更消息，通过 raft.Node.ProposeConfChange API 提交配置变化消息；从 raft.Node 中获取 Raft 算法状态机输出到 Ready 结构中，将 rd.Entries 和 rd.HardState 通过 WAL 模块持久化，将 rd.Messages 通过 rafthttp 模块，发送给其他节点。将 rd.CommittedEntries 应用到业务存储状态机。


以上就是 Raft 实现的核心流程，接下来我来和你聊聊业务存储状态机。


### 支持多存储引擎
在整体架构设计时，我和你介绍了为了使 metcd 项目能支撑多存储引擎，我们将 KVStore 进行了抽象化设计，因此我们只需要实现各个存储引擎相对应的 API 即可。这里我以 Put 接口为案例，分别给你介绍下各个存储引擎的实现。

#### boltdb
首先是 boltdb 存储引擎，它的实现如下，你也可以去10里回顾一下它的 API 和原理。
```
func (s *boltdbKVStore) Put(key, value string) error {
   s.mu.Lock()
   defer s.mu.Unlock()
   // Start a writable transaction.
   tx, err := s.db.Begin(true)
   if err != nil {
      return err
   }
   defer tx.Rollback()

   // Use the transaction...
   bucket, err := tx.CreateBucketIfNotExists([]byte("keys"))
   if err != nil {
      log.Printf("failed to put key %s, value %s, err is %v", key, value, err)
      return err
   }
   err = bucket.Put([]byte(key), []byte(value))
   if err != nil {
      log.Printf("failed to put key %s, value %s, err is %v", key, value, err)
      return err
   }

   // Commit the transaction and check for error.
   if err := tx.Commit(); err != nil {
      log.Printf("failed to commit transaction, key %s, err is %v", key, err)
      return err
   }
   log.Printf("backend:%s,put key:%s,value:%s succ", s.config.backend, key, value)
   return nil
```

### leveldb
其次是 leveldb，我们使用的是[goleveldb](https://github.com/syndtr/goleveldb)，它基于 Google 开源的 c++ [leveldb](https://github.com/google/leveldb)版本实现。它提供的常用 API 如下所示。通过 OpenFile API 创建或打开一个 leveldb 数据库。

```
db, err := leveldb.OpenFile("path/to/db", nil)
...
defer db.Close()
```
通过 DB.Get/Put/Delete API 操作数据。
```
data, err := db.Get([]byte("key"), nil)
...
err = db.Put([]byte("key"), []byte("value"), nil)
...
err = db.Delete([]byte("key"), nil)
...
```
了解其接口后，通过 goleveldb 的库，client 调用就非常简单了，下面是 metcd 项目中，leveldb 存储引擎 Put 接口的实现。

```
func (s *leveldbKVStore) Put(key, value string) error {
   err := s.db.Put([]byte(key), []byte(value), nil)
   if err != nil {
      log.Printf("failed to put key %s, value %s, err is %v", key, value, err)
      return err
   }
   log.Printf("backend:%s,put key:%s,value:%s succ", s.config.backend, key, value)
   return nil
}
```
### 读写流程
介绍完在 metcd 项目中如何使用 Raft 共识模块、支持多存储引擎后，我们再从整体上介绍下在 metcd 中写入和读取一个 key-value 的流程。

#### 写流程
当你通过如下 curl 命令发起一个写操作时，写流程如下面架构图序号所示:

```
curl -L http://127.0.0.1:3379/hello -XPUT -d world
```

client 通过 curl 发送 HTTP PUT 请求到 server；server 收到后，将消息写入到 KVStore 的 ProposeC 管道；raftNode 循环逻辑将消息通过 Raft 模块的 Propose 接口提交；Raft 模块输出 Ready 结构，server 将日志条目持久化后，并发送给其他节点；集群多数节点持久化此日志条目后，这个日志条目被提交给存储状态机 KVStore 执行；KVStore 根据启动的 backend 存储引擎名称，调用对应的 Put 接口即可。

![img](https://static001.geekbang.org/resource/image/9b/c1/9b84a7e312165de46749e1c4046fc9c1.png?wh=1920*1135)

### 读流程
当你通过如下 curl 命令发起一个读操作时，读流程如下面架构图序号所示：

```
curl -L http://127.0.0.1:3379/hello
world
```
client 通过 curl 发送 HTTP Get 请求到 server；server 收到后，根据 KVStore 的存储引擎，从后端查询出对应的 key-value 数据。

![img](https://static001.geekbang.org/resource/image/17/b2/1746fbd9e9435d8607e44bea2d2c39b2.png?wh=1920*1187)

### 小结
最后，我来总结下我们今天的内容。我这节课分别从整体架构设计和实现分析，给你介绍了如何基于 Raft 从 0 到 1 构建一个支持多存储引擎的分布式 key-value 数据库。在整体架构设计上，我给你介绍了 API 设计核心因素，它们分别是性能、易用性、开发效率、安全性、幂等性。其次我和你介绍了复制状态机的原理，它由共识模块、日志模块、存储状态机模块组成。最后我和你深入分析了多存储引擎设计，重点介绍了 leveldb 原理，它将随机写转换为顺序写日志和内存，通过一系列分层、创新的设计实现了优异的写性能，适合读少写多。在实现分析上，我和你重点介绍了 Raft 算法库的核心对象 Node API。对于一个库而言，我们重点关注的是其输入、输出接口，业务逻辑层可通过 Propose 接口提交提案，通过 Ready 结构获取 Raft 算法状态机的输出内容。其次我和你介绍了 Raft 算法库如何与 WAL 模块、Raft 日志存储模块、网络模块协作完成一个写请求。最后为了支持多存储引擎，我们分别基于 boltdb、leveldb 实现了 KVStore 相关接口操作，并通过读写流程图，从整体上为你介绍了一个读写请求在 metcd 中是如何工作的。麻雀虽小，五脏俱全。希望能通过这个迷你项目解答你对如何构建一个简易分布式 KV 服务的疑问，以及让你对 etcd 的工作原理有更深的理解。


## 19 | Kubernetes基础应用：创建一个Pod背后etcd发生了什么？

今天我将通过在 Kubernetes 集群中创建一个 Pod 的案例，为你分析 etcd 在其中发挥的作用，带你深入了解 Kubernetes 是如何使用 etcd 的。希望通过本节课，帮助你从 etcd 的角度更深入理解 Kubernetes，让你知道在 Kubernetes 集群中每一步操作的背后，etcd 会发生什么。更进一步，当你在 Kubernetes 集群中遇到 etcd 相关错误的时候，能从 etcd 角度理解错误含义，高效进行故障诊断。

### Kubernetes 基础架构
在带你详细了解 etcd 在 Kubernetes 里的应用之前，我先和你简单介绍下 Kubernetes 集群的整体架构，帮你搞清楚 etcd 在 Kubernetes 集群中扮演的角色与作用。下图是 Kubernetes 集群的架构图（引用自 [Kubernetes 官方文档](https://kubernetes.io/docs/concepts/overview/components/)），从图中你可以看到，它由 Master 节点和 Node 节点组成。

![img](https://static001.geekbang.org/resource/image/b1/c0/b13d665a0e5be852c050d09c8602e4c0.png?wh=1920*831)

控制面 Master 节点主要包含以下组件：kube-apiserver，负责对外提供集群各类资源的增删改查及 Watch 接口，它是 Kubernetes 集群中各组件数据交互和通信的枢纽。kube-apiserver 在设计上可水平扩展，高可用 Kubernetes 集群中一般多副本部署。当收到一个创建 Pod 写请求时，它的基本流程是对请求进行认证、限速、授权、准入机制等检查后，写入到 etcd 即可。kube-scheduler 是调度器组件，负责集群 Pod 的调度。基本原理是通过监听 kube-apiserver 获取待调度的 Pod，然后基于一系列筛选和评优算法，为 Pod 分配最佳的 Node 节点。kube-controller-manager 包含一系列的控制器组件，比如 Deployment、StatefulSet 等控制器。控制器的核心思想是监听、比较资源实际状态与期望状态是否一致，若不一致则进行协调工作使其最终一致。etcd 组件，Kubernetes 的元数据存储。

Node 节点主要包含以下组件：kubelet，部署在每个节点上的 Agent 的组件，负责 Pod 的创建运行。基本原理是通过监听 APIServer 获取分配到其节点上的 Pod，然后根据 Pod 的规格详情，调用运行时组件创建 pause 和业务容器等。kube-proxy，部署在每个节点上的网络代理组件。基本原理是通过监听 APIServer 获取 Service、Endpoint 等资源，基于 Iptables、IPVS 等技术实现数据包转发等功能。

从 Kubernetes 基础架构介绍中你可以看到，kube-apiserver 是唯一直接与 etcd 打交道的组件，各组件都通过 kube-apiserver 实现数据交互，它们极度依赖 kube-apiserver 提供的资源变化监听机制。而 kube-apiserver 对外提供的监听机制，也正是由我们基础篇08中介绍的 etcd Watch 特性提供的底层支持。


### 创建 Pod 案例
接下来我们就以在 Kubernetes 集群中创建一个 nginx 服务为例，通过这个案例来详细分析 etcd 在 Kubernetes 集群创建 Pod 背后是如何工作的。下面是创建一个 nginx 服务的 YAML 文件，Workload 是 Deployment，期望的副本数是 1。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

假设此 YAML 文件名为 nginx.yaml，首先我们通过如下的 kubectl create -f nginx.yml 命令创建 Deployment 资源。

```
$ kubectl create -f nginx.yml
deployment.apps/nginx-deployment created
```

创建之后，我们立刻通过如下命令，带标签查询 Pod，输出如下：

```
$ kubectl get po -l app=nginx
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-756d9fd5f9-fkqnf   1/1     Running   0          8s
```
那么在 kubectl create 命令发出，nginx Deployment 资源成功创建的背后，kube-apiserver 是如何与 etcd 打交道的呢？ 它是通过什么接口安全写入资源到 etcd 的？同时，使用 kubectl 带标签查询 Pod 背后，kube-apiserver 是直接从缓存读取还是向 etcd 发出一个线性读或串行读请求呢？ 若同 namespace 下存在大量的 Pod，此操作性能又是怎样的呢?接下来我就和你聊聊 kube-apiserver 收到创建和查询请求后，是如何与 etcd 交互的。

### kube-apiserver 请求执行链路
kube-apiserver 作为 Kubernetes 集群交互的枢纽、对外提供 API 供用户访问的组件，因此保障集群安全、保障本身及后端 etcd 的稳定性的等重任也是非它莫属。比如校验创建请求发起者是否合法、是否有权限操作相关资源、是否出现 Bug 产生大量写和读请求等。[下图是 kube-apiserver 的请求执行链路](https://speakerdeck.com/sttts/kubernetes-api-codebase-tour?slide=18)（引用自 sttts 分享的 PDF），当收到一个请求后，它主要经过以下处理链路来完成以上若干职责后，才能与 etcd 交互。


核心链路如下：认证模块，校验发起的请求的用户身份是否合法。支持多种方式，比如 x509 客户端证书认证、静态 token 认证、webhook 认证等。限速模块，对请求进行简单的限速，默认读 400/s 写 200/s，不支持根据请求类型进行分类、按优先级限速，存在较多问题。Kubernetes 1.19 后已新增 Priority and Fairness 特性取代它，它支持将请求重要程度分类进行限速，支持多租户，可有效保障 Leader 选举之类的高优先级请求得到及时响应，能防止一个异常 client 导致整个集群被限速。审计模块，可记录用户对资源的详细操作行为。授权模块，检查用户是否有权限对其访问的资源进行相关操作。支持多种方式，RBAC(Role-based access control)、ABAC(Attribute-based access control)、Webhhook 等。Kubernetes 1.12 版本后，默认授权机制使用的 RBAC。准入控制模块，提供在访问资源前拦截请求的静态和动态扩展能力，比如要求镜像的拉取策略始终为 AlwaysPullImages。

![img](https://static001.geekbang.org/resource/image/56/bc/561f38086df49d17ee4e12ec3c5220bc.png?wh=1920*1078)



经过上面一系列的模块检查后，这时 kube-apiserver 就开始与 etcd 打交道了。在了解 kube-apiserver 如何将我们创建的 Deployment 资源写入到 etcd 前，我先和你介绍下 Kubernetes 的资源是如何组织、存储在 etcd 中。

### Kubernetes 资源存储格式
我们知道 etcd 仅仅是个 key-value 存储，但是在 Kubernetes 中存在各种各样的资源，并提供了以下几种灵活的资源查询方式：按具体资源名称查询，比如 PodName、kubectl get po/PodName。按 namespace 查询，获取一个 namespace 下的所有 Pod，比如 kubectl get po -n kube-system。按标签名，标签是极度灵活的一种方式，你可以为你的 Kubernetes 资源打上各种各样的标签，比如上面案例中的 kubectl get po -l app=nginx。

你知道以上这几种查询方式它们的性能优劣吗？假设你是 Kubernetes 开发者，你会如何设计存储格式来满足以上功能点？首先是按具体资源名称查询。它本质就是个 key-value 查询，只需要写入 etcd 的 key 名称与资源 key 一致即可。其次是按 namespace 查询。这种查询也并不难。因为我们知道 etcd 支持范围查询，若 key 名称前缀包含 namespace、资源类型，查询的时候指定 namespace 和资源类型的组合的最小开始区间、最大结束区间即可。最后是标签名查询。这种查询方式非常灵活，业务可随时添加、删除标签，各种标签可相互组合。实现标签查询的办法主要有以下两种：


方案一，在 etcd 中存储标签数据，实现通过标签可快速定位（时间复杂度 O(1)）到具体资源名称。然而一个标签可能容易实现，但是在 Kubernetes 集群中，它支持按各个标签组合查询，各个标签组合后的数量相当庞大。在 etcd 中维护各种标签组合对应的资源列表，会显著增加 kube-apiserver 的实现复杂度，导致更频繁的 etcd 写入。方案二，在 etcd 中不存储标签数据，而是由 kube-apiserver 通过范围遍历 etcd 获取原始数据，然后基于用户指定标签，来筛选符合条件的资源返回给 client。此方案优点是实现简单，但是大量标签查询可能会导致 etcd 大流量等异常情况发生。

那么 Kubernetes 集群选择的是哪种实现方式呢?下面是一个 Kubernetes 集群中的 coredns 一系列资源在 etcd 中的存储格式：

```
/registry/clusterrolebindings/system:coredns
/registry/clusterroles/system:coredns
/registry/configmaps/kube-system/coredns
/registry/deployments/kube-system/coredns
/registry/events/kube-system/coredns-7fcc6d65dc-6njlg.1662c287aabf742b
/registry/events/kube-system/coredns-7fcc6d65dc-6njlg.1662c288232143ae
/registry/pods/kube-system/coredns-7fcc6d65dc-jvj26
/registry/pods/kube-system/coredns-7fcc6d65dc-mgvtb
/registry/pods/kube-system/coredns-7fcc6d65dc-whzq9
/registry/replicasets/kube-system/coredns-7fcc6d65dc
/registry/secrets/kube-system/coredns-token-hpqbt
/registry/serviceaccounts/kube-system/coredns
```


从中你可以看到，一方面 Kubernetes 资源在 etcd 中的存储格式由 prefix + "/" + 资源类型 + "/" + namespace + "/" + 具体资源名组成，基于 etcd 提供的范围查询能力，非常简单地支持了按具体资源名称查询和 namespace 查询。kube-apiserver 提供了如下参数给你配置 etcd prefix，并支持将资源存储在多个 etcd 集群。


```
--etcd-prefix string     Default: "/registry"
The prefix to prepend to all resource paths in etcd.
--etcd-servers stringSlice
List of etcd servers to connect with (scheme://ip:port), comma separated.
--etcd-servers-overrides stringSlice
Per-resource etcd servers overrides, comma separated. The individual override format: group/resource#servers, where servers are URLs, 
semicolon separated.
```
另一方面，我们未看到任何标签相关的 key。Kubernetes 实现标签查询的方式显然是方案二，即由 kube-apiserver 通过范围遍历 etcd 获取原始数据，然后基于用户指定标签，来筛选符合条件的资源返回给 client（资源 key 的 value 中记录了资源 YAML 文件内容等，如标签）。也就是当你执行"kubectl get po -l app=nginx"命令，按标签查询 Pod 时，它会向 etcd 发起一个范围遍历整个 default namespace 下的 Pod 操作。

```
$ kubectl get po -l app=nginx -v 8
I0301 23:45:25.597465   32411 loader.go:359] Config loaded from file /root/.kube/config
I0301 23:45:25.603182   32411 round_trippers.go:416] GET https://ip:port/api/v1/namespaces/default/pods?
labelSelector=app%3Dnginx&limit=500
```
etcd 收到的请求日志如下，由此可见当一个 namespace 存在大量 Pod 等资源时，若频繁通过 kubectl，使用标签查询 Pod 等资源，后端 etcd 将出现较大的压力。

```
{
    "level":"debug",
    "ts":"2021-03-01T23:45:25.609+0800",
    "caller":"v3rpc/interceptor.go:181",
    "msg":"request stats",
    "start time":"2021-03-01T23:45:25.608+0800",
    "time spent":"1.414135ms",
    "remote":"127.0.0.1:44664",
    "response type":"/etcdserverpb.KV/Range",
    "request count":0,
    "request size":61,
    "response count":11,
    "response size":81478,
    "request content":"key:"/registry/pods/default/" range_end:"/registry/pods/default0" limit:500 "
}
```
了解完 Kubernetes 资源的存储格式后，我们再看看 nginx Deployment 资源是如何由 kube-apiserver 写入 etcd 的。


### 通用存储模块
kube-apiserver 启动的时候，会将每个资源的 APIGroup、Version、Resource Handler 注册到路由上。当请求经过认证、限速、授权、准入控制模块检查后，请求就会被转发到对应的资源逻辑进行处理。同时，kube-apiserver 实现了类似数据库 ORM 机制的通用资源存储机制，提供了对一个资源创建、更新、删除前后的 hook 能力，将其封装成策略接口。当你新增一个资源时，你只需要编写相应的创建、更新、删除等策略即可，不需要写任何 etcd 的 API。下面是 kube-apiserver 通用存储模块的创建流程图：

![img](https://static001.geekbang.org/resource/image/4d/09/4d8fa0f1d6afd89cf6463cf22c56b709.png?wh=1920*1178)

从图中你可以看到，创建一个资源主要由 BeforeCreate、Storage.Create 以及 AfterCreate 三大步骤组成。当收到创建 nginx Deployment 请求后，通用存储模块首先会回调各个资源自定义实现的 BeforeCreate 策略，为资源写入 etcd 做一些初始化工作。下面是 Deployment 资源的创建策略实现，它会进行将 deployment.Generation 设置为 1 等操作。


```
// PrepareForCreate clears fields that are not allowed to be set by end users on creation.
func (deploymentStrategy) PrepareForCreate(ctx context.Context, obj runtime.Object) {
   deployment := obj.(*apps.Deployment)
   deployment.Status = apps.DeploymentStatus{}
   deployment.Generation = 1

   pod.DropDisabledTemplateFields(&deployment.Spec.Template, nil)
}
```
执行完 BeforeCreate 策略后，它就会执行 Storage.Create 接口，也就是由它真正开始调用底层存储模块 etcd3，将 nginx Deployment 资源对象写入 etcd。那么 Kubernetes 是使用 etcd Put 接口写入资源 key-value 的吗？如果是，那要如何防止同名资源并发创建被覆盖的问题？


### 资源安全创建及更新
我们知道 etcd 提供了 Put 和 Txn 接口给业务添加 key-value 数据，但是 Put 接口在并发场景下若收到 key 相同的资源创建，就会导致被覆盖。因此 Kubernetes 很显然无法直接通过 etcd Put 接口来写入数据。而我们09节中介绍的 etcd 事务接口 Txn，它正是为了多 key 原子更新、并发操作安全性等而诞生的，它提供了丰富的冲突检查机制。Kubernetes 集群使用的正是事务 Txn 接口来防止并发创建、更新被覆盖等问题。当执行完 BeforeCreate 策略后，这时 kube-apiserver 就会调用 Storage 的模块的 Create 接口写入资源。1.6 版本后的 Kubernete 集群默认使用的存储是 etcd3，它的创建接口简要实现如下：



```
// Create implements storage.Interface.Create.
func (s *store) Create(ctx context.Context, key string, obj, out runtime.Object, ttl uint64) error {
   ......
   key = path.Join(s.pathPrefix, key)

   opts, err := s.ttlOpts(ctx, int64(ttl))
   if err != nil {
      return err
   }

   newData, err := s.transformer.TransformToStorage(data, authenticatedDataString(key))
   if err != nil {
      return storage.NewInternalError(err.Error())
   }

   startTime := time.Now()
   txnResp, err := s.client.KV.Txn(ctx).If(
      notFound(key),
   ).Then(
      clientv3.OpPut(key, string(newData), opts...),
   ).Commit

```

从上面的代码片段中，我们可以得出首先它会按照我们介绍的 Kubernetes 资源存储格式拼接 key。然后若 TTL 非 0，它会根据 TTL 从 leaseManager 获取可复用的 Lease ID。Kubernetes 集群默认若不同 key（如 Kubernetes 的 Event 资源对象）的 TTL 差异在 1 分钟内，可复用同一个 Lease ID，避免大量 Lease 影响 etcd 性能和稳定性。其次若开启了数据加密，在写入 etcd 前数据还将按加密算法进行转换工作。最后就是使用 etcd 的 Txn 接口，向 etcd 发起一个创建 deployment 资源的 Txn 请求。那么 etcd 收到 kube-apiserver 的请求是长什么样子的呢？下面是 etcd 收到创建 nginx deployment 资源的请求日志：

```
{
    "level":"debug",
    "ts":"2021-02-11T09:55:45.914+0800",
    "caller":"v3rpc/interceptor.go:181",
    "msg":"request stats",
    "start time":"2021-02-11T09:55:45.911+0800",
    "time spent":"2.697925ms",
    "remote":"127.0.0.1:44822",
    "response type":"/etcdserverpb.KV/Txn",
    "request count":1,
    "request size":479,
    "response count":0,
    "response size":44,
    "request content":"compare:<target:MOD key:"/registry/deployments/default/nginx-deployment" mod_revision:0 > success:<request_put:<key:"/registry/deployments/default/nginx-deployment" value_size:421 >> failure:<>"
}
```

从这个请求日志中，你可以得到以下信息：请求的模块和接口，KV/Txn；key 路径，/registry/deployments/default/nginx-deployment，由 prefix + "/" + 资源类型 + "/" + namespace + "/" + 具体资源名组成；安全的并发创建检查机制，mod_revision 为 0 时，也就是此 key 不存在时，才允许执行 put 更新操作。

通过 Txn 接口成功将数据写入到 etcd 后，kubectl create -f nginx.yml 命令就执行完毕，返回给 client 了。在以上介绍中你可以看到，kube-apiserver 并没有任何逻辑去真正创建 Pod，但是为什么我们可以马上通过 kubectl get 命令查询到新建并成功运行的 Pod 呢？这就涉及到了基础架构图中的控制器、调度器、Kubelet 等组件。下面我就为你浅析它们是如何基于 etcd 提供的 Watch 机制工作，最终实现创建 Pod、调度 Pod、运行 Pod 的。

### Watch 机制在 Kubernetes 中应用
正如我们基础架构中所介绍的，kube-controller-manager 组件中包含一系列 WorkLoad 的控制器。Deployment 资源就由其中的 Deployment 控制器来负责的，那么它又是如何感知到新建 Deployment 资源，最终驱动 ReplicaSet 控制器创建出 Pod 的呢？获取数据变化的方案，主要有轮询和推送两种方案组成。轮询会产生大量 expensive request，并且存在高延时。而 etcd Watch 机制提供的流式推送能力，赋予了 kube-apiserver 对外提供数据监听能力。我们知道在 etcd 中版本号是个逻辑时钟，随着 client 对 etcd 的增、删、改操作而全局递增，它被广泛应用在 MVCC、事务、Watch 特性中。尤其是在 Watch 特性中，版本号是数据增量同步的核心。当 client 因网络等异常出现连接闪断后，它就可以通过版本号从 etcd server 中快速获取异常后的事件，无需全量同步。那么在 Kubernetes 集群中，它提供了什么概念来实现增量监听逻辑呢？答案是 Resource Version。


### Resource Version 与 etcd 版本号
Resource Version 是 Kubernetes API 中非常重要的一个概念，顾名思义，它是一个 Kubernetes 资源的内部版本字符串，client 可通过它来判断资源是否发生了变化。同时，你可以在 Get、List、Watch 接口中，通过指定 Resource Version 值来满足你对数据一致性、高性能等诉求。那么 Resource Version 有哪些值呢？跟 etcd 版本号是什么关系？

下面我分别以 Get 和 Watch 接口中的 Resource Version 参数值为例，为你剖析它与 etcd 的关系。在 Get 请求查询案例中，ResourceVersion 主要有以下这三种取值：

第一种是未指定 ResourceVersion，默认空字符串。kube-apiserver 收到一个此类型的读请求后，它会向 etcd 发出共识读 / 线性读请求获取 etcd 集群最新的数据。第二种是设置 ResourceVersion="0"，赋值字符串 0。kube-apiserver 收到此类请求时，它可能会返回任意资源版本号的数据，但是优先返回较新版本。一般情况下它直接从 kube-apiserver 缓存中获取数据返回给 client，有可能读到过期的数据，适用于对数据一致性要求不高的场景。第三种是设置 ResourceVersion 为一个非 0 的字符串。kube-apiserver 收到此类请求时，它会保证 Cache 中的最新 ResourceVersion 大于等于你传入的 ResourceVersion，然后从 Cache 中查找你请求的资源对象 key，返回数据给 client。基本原理是 kube-apiserver 为各个核心资源（如 Pod）维护了一个 Cache，通过 etcd 的 Watch 机制来实时更新 Cache。当你的 Get 请求中携带了非 0 的 ResourceVersion，它会等待缓存中最新 ResourceVersion 大于等于你 Get 请求中的 ResoureVersion，若满足条件则从 Cache 中查询数据，返回给 client。若不满足条件，它最多等待 3 秒，若超过 3 秒，Cache 中的最新 ResourceVersion 还小于 Get 请求中的 ResourceVersion，就会返回 ResourceVersionTooLarge 错误给 client。

你要注意的是，若你使用的 Get 接口，那么 kube-apiserver 会取资源 key 的 ModRevision 字段填充 Kubernetes 资源的 ResourceVersion 字段（v1.meta/ObjectMeta.ResourceVersion）。若你使用的是 List 接口，kube-apiserver 会在查询时，使用 etcd 当前版本号填充 ListMeta.ResourceVersion 字段（v1.meta/ListMeta.ResourceVersion）。那么当我们执行 kubectl get po 查询案例时，它的 ResouceVersion 是什么取值呢? 查询的是 kube-apiserver 缓存还是 etcd 最新共识数据?如下所示，你可以通过指定 kubectl 日志级别为 6，观察它向 kube-apiserver 发出的请求参数。从下面请求日志里你可以看到，默认是未指定 Resource Version，也就是会发出一个共识读 / 线性读请求给 etcd，获取 etcd 最新共识数据。

```
kubectl get po -l app=nginx -v 6
4410 loader.go:359] Config loaded from file /root/.kube/config
4410 round_trippers.go:438] GET https://*.*.*.*:*/api/v1/namespaces/default/pods?labelSelector=app%3Dnginx&limit=500 200 OK in 8 milliseconds
```
这里要提醒下你，在规模较大的集群中，尽量不要使用 kubectl 频繁查询资源。正如我们上面所分析的，它会直接查询 etcd 数据，可能会产生大量的 expensive request 请求，之前我就有见过业务这样用，然后导致了集群不稳定。介绍完查询案例后，我们再看看 Watch 案例中，它的不同取值含义是怎样的呢?它同样含有查询案例中的三种取值，官方定义的含义分别如下：

未指定 ResourceVersion，默认空字符串。一方面为了帮助 client 建立初始状态，它会将当前已存在的资源通过 Add 事件返回给 client。另一方面，它会从 etcd 当前版本号开始监听，后续新增写请求导致数据变化时可及时推送给 client。设置 ResourceVersion="0"，赋值字符串 0。它同样会帮助 client 建立初始状态，但是它会从任意版本号开始监听（当前 kube-apiserver 的实现指定 ResourceVersion=0 和不指定行为一致，在获取初始状态后，都会从 cache 最新的 ResourceVersion 开始监听），这种场景可能会导致集群返回陈旧的数据。设置 ResourceVersion 为一个非 0 的字符串。从精确的版本号开始监听数据，它只会返回大于等于精确版本号的变更事件。

Kubernetes 的控制器组件就基于以上的 Watch 特性，在快速感知到新建 Deployment 资源后，进入一致性协调逻辑，创建 ReplicaSet 控制器，整体交互流程如下所示。

![img](https://static001.geekbang.org/resource/image/89/54/89c610a5e5bc2bf5eda466a5a0e18e54.png?wh=1740*1456)


Deployment 控制器创建 ReplicaSet 资源对象的日志如下所示。

```
{
    "level":"debug",
    "ts":"2021-02-11T09:55:45.923+0800",
    "caller":"v3rpc/interceptor.go:181",
    "msg":"request stats",
    "start time":"2021-02-11T09:55:45.917+0800",
    "time spent":"5.922089ms",
    "remote":"127.0.0.1:44828",
    "response type":"/etcdserverpb.KV/Txn",
    "request count":1,
    "request size":766,
    "response count":0,
    "response size":44,
    "request content":"compare:<target:MOD key:"/registry/replicasets/default/nginx-deployment-756d9fd5f9" mod_revision:0 > success:<request_put:<key:"/registry/replicasets/default/nginx-deployment-756d9fd5f9" value_size:697 >> failure:<>"
}
```
真正创建 Pod 则是由 ReplicaSet 控制器负责，它同样基于 Watch 机制感知到新的 RS 资源创建后，发起请求创建 Pod，确保实际运行 Pod 数与期望一致。
```
{
    "level":"debug",
    "ts":"2021-02-11T09:55:46.023+0800",
    "caller":"v3rpc/interceptor.go:181",
    "msg":"request stats",
    "start time":"2021-02-11T09:55:46.019+0800",
    "time spent":"3.519326ms",
    "remote":"127.0.0.1:44664",
    "response type":"/etcdserverpb.KV/Txn",
    "request count":1,
    "request size":822,
    "response count":0,
    "response size":44,
    "request content":"compare:<target:MOD key:"/registry/pods/default/nginx-deployment-756d9fd5f9-x6r6q" mod_revision:0 > success:<request_put:<key:"/registry/pods/default/nginx-deployment-756d9fd5f9-x6r6q" value_size:754 >> failure:<>"
}
```
在这过程中也产生了若干 Event，下面是 etcd 收到新增 Events 资源的请求，你可以看到 Event 事件 key 关联了 Lease，这个 Lease 正是由我上面所介绍的 leaseManager 所负责创建。


```
{
    "level":"debug",
    "ts":"2021-02-11T09:55:45.930+0800",
    "caller":"v3rpc/interceptor.go:181",
    "msg":"request stats",
    "start time":"2021-02-11T09:55:45.926+0800",
    "time spent":"3.259966ms",
    "remote":"127.0.0.1:44632",
    "response type":"/etcdserverpb.KV/Txn",
    "request count":1,
    "request size":449,
    "response count":0,
    "response size":44,
    "request content":"compare:<target:MOD key:"/registry/events/default/nginx-deployment.16628eb9f79e0ab0" mod_revision:0 > success:<request_put:<key:"/registry/events/default/nginx-deployment.16628eb9f79e0ab0" value_size:369 lease:5772338802590698925 >> failure:<>"
}
```
Pod 创建出来后，这时 kube-scheduler 监听到待调度的 Pod，于是为其分配 Node，通过 kube-apiserver 的 Bind 接口，将调度后的节点 IP 绑定到 Pod 资源上。kubelet 通过同样的 Watch 机制感知到新建的 Pod 后，发起 Pod 创建流程即可。以上就是当我们在 Kubernetes 集群中创建一个 Pod 后，Kubernetes 和 etcd 之间交互的简要分析。

### 小结
最后我们来小结下今天的内容。我通过一个创建 Pod 案例，首先为你解读了 Kubernetes 集群的 etcd 存储格式，每个资源的保存路径为 prefix + "/" + 资源类型 + "/" + namespace + "/" + 具体资源名组成。结合 etcd3 的范围查询，可快速实现按 namesapace、资源名称查询。按标签查询则是通过 kube-apiserver 遍历指定 namespace 下的资源实现的，若未从 kube-apiserver 的 Cache 中查询，请求较频繁，很可能导致 etcd 流量较大，出现不稳定。随后我和你介绍了 kube-apiserver 的通用存储模块，它通过在创建、查询、删除、更新操作前增加一系列的 Hook 机制，实现了新增任意资源只需编写相应的 Hook 策略即可。我还重点和你介绍了创建接口，它主要由拼接 key、获取 Lease ID、数据转换、写入 etcd 组成，重点是它通过使用事务接口实现了资源的安全创建及更新。最后我给你讲解了 Resoure Version 在 Kubernetes 集群中的大量应用，重点和你分析了 Get 和 Watch 请求案例中的 Resource Version 含义，帮助你了解 Resource Version 本质，让你能根据业务场景和对一致性的容忍度，正确的使用 Resource Version 以满足业务诉求。

## 20 | Kubernetes高级应用：如何优化业务场景使etcd能支撑上万节点集群？

你知道吗？ 虽然 Kubernetes 社区官网文档目前声称支持最大集群节点数为 5000，但是云厂商已经号称支持 15000 节点的 Kubernetes 集群了，那么为什么一个小小的 etcd 能支撑 15000 节点 Kubernetes 集群呢？今天我就和你聊聊为了支撑 15000 节点，Kubernetes 和 etcd 的做的一系列优化。我将重点和你分析 Kubernetes 针对 etcd 的瓶颈是如何从应用层采取一系列优化措施，去解决大规模集群场景中各个痛点。当你遇到 etcd 性能瓶颈时，希望这节课介绍的大规模 Kubernetes 集群的最佳实践经验和优化技术，能让你获得启发，帮助你解决类似问题。

### 大集群核心问题分析
在大规模 Kubernetes 集群中会遇到哪些问题呢？大规模 Kubernetes 集群的外在表现是节点数成千上万，资源对象数量高达几十万。本质是更频繁地查询、写入更大的资源对象。首先是查询相关问题。在大集群中最重要的就是如何最大程度地减少 expensive request。因为对几十万级别的对象数量来说，按标签、namespace 查询 Pod，获取所有 Node 等场景时，很容易造成 etcd 和 kube-apiserver OOM 和丢包，乃至雪崩等问题发生。其次是写入相关问题。Kubernetes 为了维持上万节点的心跳，会产生大量写请求。而按照我们基础篇介绍的 etcd MVCC、boltdb、线性读等原理，etcd 适用场景是读多写少，大量写请求可能会导致 db size 持续增长、写性能达到瓶颈被限速、影响读性能。最后是大资源对象相关问题。etcd 适合存储较小的 key-value 数据，etcd 本身也做了一系列硬限制，比如 key 的 value 大小默认不能超过 1.5MB。本讲我就和你重点分析下 Kubernetes 是如何优化以上问题，以实现支撑上万节点的。以及我会简单和你讲下 etcd 针对 Kubernetes 场景做了哪些优化。


### 如何减少 expensive request
首先是第一个问题，Kubernetes 如何减少 expensive request？在这个问题中，我将 Kubernetes 解决此问题的方案拆分成几个核心点和你分析。

#### 分页
首先 List 资源操作是个基本功能点。各个组件在启动的时候，都不可避免会产生 List 操作，从 etcd 获取集群资源数据，构建初始状态。因此优化的第一步就是要避免一次性读取数十万的资源操作。解决方案是 Kubernetes List 接口支持分页特性。分页特性依赖底层存储支持，早期的 etcd v2 并未支持分页被饱受诟病，非常容易出现 kube-apiserver 大流量、高负载等问题。在 etcd v3 中，实现了指定返回 Limit 数量的范围查询，因此也赋能 kube-apiserver 对外提供了分页能力。如下所示，在 List 接口的 ListOption 结构体中，Limit 和 Continue 参数就是为了实现分页特性而增加的。Limit 表示一次 List 请求最多查询的对象数量，一般为 500。如果实际对象数量大于 Limit，kube-apiserver 则会更新 ListMeta 的 Continue 字段，client 发起的下一个 List 请求带上这个字段就可获取下一批对象数量。直到 kube-apiserver 返回空的 Continue 值，就获取完成了整个对象结果集。

```
// ListOptions is the query options to a standard REST 
list call.
type ListOptions struct {
   ...
   Limit int64 `json:"limit,omitempty" 
protobuf:"varint,7,opt,name=limit"`
   Continue string `json:"continue,omitempty" 
protobuf:"bytes,8,opt,name=continue"`
}
```
了解完 kube-apiserver 的分页特性后，我们接着往下看 Continue 字段具体含义，以及它是如何影响 etcd 查询结果的。我们知道 etcd 分页是通过范围查询和 Limit 实现，ListOption 中的 Limit 对应 etcd 查询接口中的 Limit 参数。你可以大胆猜测下，Continue 字段是不是跟查询的范围起始 key 相关呢？Continue 字段的确包含查询范围的起始 key，它本质上是个结构体，还包含 APIVersion 和 ResourceVersion。你之所以看到的是一个奇怪字符串，那是因为 kube-apiserver 使用 base64 库对其进行了 URL 编码，下面是它的原始结构体。


```
type continueToken struct {
   APIVersion      string `json:"v"`
   ResourceVersion int64  `json:"rv"`
   StartKey        string `json:"start"`
}
```

当 kube-apiserver 收到带 Continue 的分页查询时，解析 Continue，获取 StartKey、ResourceVersion，etcd 查询 Range 接口指定 startKey，增加 clienv3.WithRange、clientv3.WithLimit、clientv3.WithRev 即可。当你通过分页多次查询 Kubernetes 资源对象，得到的最终结果集合与不带 Limit 查询结果是一致的吗？kube-apiserver 是如何保证分页查询的一致性呢？ 这个问题我把它作为了思考题，我们一起讨论。

### 资源按 namespace 拆分
通过分页特性提供机制避免一次拉取大量资源对象后，接下来就是业务最佳实践上要避免同 namespace 存储大量资源，尽量将资源对象拆分到不同 namespace 下。为什么拆分到不同 namespace 下有助于提升性能呢?正如我在19中所介绍的，Kubernetes 资源对象存储在 etcd 中的 key 前缀包含 namespace，因此它相当于是个高效的索引字段。etcd treeIndex 模块从 B-tree 中匹配前缀时，可快速过滤出符合条件的 key-value 数据。Kubernetes 社区承诺[SLO](https://github.com/kubernetes/community/blob/master/sig-scalability/slos/slos.md)达标的前提是，你在使用 Kubernetes 集群过程中必须合理配置集群和使用扩展特性，并遵循一[系列条件限制](https://github.com/kubernetes/community/blob/master/sig-scalability/configs-and-limits/thresholds.md)（比如同 namespace 下的 Service 数量不超过 5000 个）。

### Informer 机制
各组件启动发起一轮 List 操作加载完初始状态数据后，就进入了控制器的一致性协调逻辑。在一致性协调逻辑中，在 19 讲 Kubernetes 基础篇中，我和你介绍了 Kubernetes 使用的是 Watch 特性来获取数据变化通知，而不是 List 定时轮询，这也是减少 List 操作一大核心策略。Kubernetes 社区在 client-go 项目中提供了一个通用的 Informer 组件来负责 client 与 kube-apiserver 进行资源和事件同步，显著降低了开发者使用 Kubernetes API、开发高性能 Kubernetes 扩展组件的复杂度。Informer 机制的 Reflector 封装了 Watch、List 操作，结合本地 Cache、Indexer，实现了控制器加载完初始状态数据后，接下来的其他操作都只需要从本地缓存读取，极大降低了 kube-apiserver 和 etcd 的压力。下面是 Kubernetes 社区给出的一个控制器使用 Informer 机制的架构图。黄色部分是控制器相关基础组件，蓝色部分是 client-go 的 Informer 机制的组件，它由 Reflector、Queue、Informer、Indexer、Thread safe store(Local Cache) 组成。

![img](https://static001.geekbang.org/resource/image/fb/99/fb7caaa37a6a860422825d2199217899.png?wh=1058*794)

Informer 机制的基本工作流程如下：client 启动或与 kube-apiserver 出现连接中断再次 Watch 时，报"too old resource version"等错误后，通过 Reflector 组件的 List 操作，从 kube-apiserver 获取初始状态数据，随后通过 Watch 机制实时监听数据变化。收到事件后添加到 Delta FIFO 队列，由 Informer 组件进行处理。Informer 将 delta FIFO 队列中的事件转发给 Indexer 组件，Indexer 组件将事件持久化存储在本地的缓存中。控制器开发者可通过 Informer 组件注册 Add、Update、Delete 事件的回调函数。Informer 组件收到事件后会回调业务函数，比如典型的控制器使用场景，一般是将各个事件添加到 WorkQueue 中，控制器的各个协调 goroutine 从队列取出消息，解析 key，通过 key 从 Informer 机制维护的本地 Cache 中读取数据。

通过以上流程分析，你可以发现除了启动、连接中断等场景才会触发 List 操作，其他时候都是从本地 Cache 读取。那连接中断等场景为什么触发 client List 操作呢？

### Watch bookmark 机制
要搞懂这个问题，你得了解 kube-apiserver Watch 特性的原理。

接下来我就和你介绍下 Kubernetes 的 Watch 特性。我们知道 Kubernetes 通过全局递增的 Resource Version 来实现增量数据同步逻辑，尽量避免连接中断等异常场景下 client 发起全量 List 同步操作。那么在什么场景下会触发全量 List 同步操作呢？这就取决于 client 请求的 Resource Version 以及 kube-apiserver 中是否还保存了相关的历史版本数据。在08Watch 特性中，我和你提到实现历史版本数据存储两大核心机制，滑动窗口和 MVCC。与 etcd v3 使用 MVCC 机制不一样的是，Kubernetes 采用的是滑动窗口机制。kube-apiserver 的滑动窗口机制是如何实现的呢?

它通过为每个类型资源（Pod,Node 等）维护一个 cyclic buffer，来存储最近的一系列变更事件实现。下面 Kubernetes 核心的 watchCache 结构体中的 cache 数组、startIndex、endIndex 就是用来实现 cyclic buffer 的。滑动窗口中的第一个元素就是 cache[ startIndex%capacity]，最后一个元素则是 cache[ endIndex%capacity]。

```
// watchCache is a "sliding window" (with a limited capacity) of objects
// observed from a watch.
type watchCache struct {
   sync.RWMutex

   // Condition on which lists are waiting for the fresh enough
   // resource version.
   cond *sync.Cond

   // Maximum size of history window.
   capacity int

   // upper bound of capacity since event cache has a dynamic size.
   upperBoundCapacity int

   // lower bound of capacity since event cache has a dynamic size.
   lowerBoundCapacity int

   // cache is used a cyclic buffer - its first element (with the smallest
   // resourceVersion) is defined by startIndex, its last element is defined
   // by endIndex (if cache is full it will be startIndex + capacity).
   // Both startIndex and endIndex can be greater than buffer capacity -
   // you should always apply modulo capacity to get an index in cache array.
   cache      []*watchCacheEvent
   startIndex int
   endIndex   int

   // store will effectively support LIST operation from the "end of cache
   // history" i.e. from the moment just after the newest cached watched event.
   // It is necessary to effectively allow clients to start watching at now.
   // NOTE: We assume that <store> is thread-safe.
   store cache.Indexer

   // ResourceVersion up to which the watchCache is propagated.
   resourceVersion uint64
}
```
下面我以 Pod 资源的历史事件滑动窗口为例，和你聊聊它在什么场景可能会触发 client 全量 List 同步操作。如下图所示，kube-apiserver 启动后，通过 List 机制，加载初始 Pod 状态数据，随后通过 Watch 机制监听最新 Pod 数据变化。当你不断对 Pod 资源进行增加、删除、修改后，携带新 Resource Version（简称 RV）的 Pod 事件就会不断被加入到 cyclic buffer。假设 cyclic buffer 容量为 100，RV1 是最小的一个 Watch 事件的 Resource Version，RV 100 是最大的一个 Watch 事件的 Resource Version。当版本号为 RV101 的 Pod 事件到达时，RV1 就会被淘汰，kube-apiserver 维护的 Pod 最小版本号就变成了 RV2。然而在 Kubernetes 集群中，不少组件都只关心 cyclic buffer 中与自己相关的事件。

![img](https://static001.geekbang.org/resource/image/29/29/29deb02b3724edef274ce71d6a758b29.png?wh=1920*986)

比如图中的 kubelet 只关注运行在自己节点上的 Pod，假设只有 RV1 是它关心的 Pod 事件版本号，在未实现 Watch bookmark 特性之前，其他 RV2 到 RV101 的事件是不会推送给它的，因此它内存中维护的 Resource Version 依然是 RV1。若此 kubelet 随后与 kube-apiserver 连接出现异常，它将使用版本号 RV1 发起 Watch 重连操作。但是 kube-apsierver cyclic buffer 中的 Pod 最小版本号已是 RV2，因此会返回"too old resource version"错误给 client，client 只能发起 List 操作，在获取到最新版本号后，才能重新进入监听逻辑。那么我们能否定时将最新的版本号推送给各个 client 来解决以上问题呢?是的，这就是 Kubernetes 的 Watch bookmark 机制核心思想。即使队列中无 client 关注的更新事件，Informer 机制的 Reflector 组件中 Resource Version 也需要更新。Watch bookmark 机制通过新增一个 bookmark 类型的事件来实现的。kube-apiserver 会通过定时器将各类型资源最新的 Resource Version 推送给 kubelet 等 client，在 client 与 kube-apiserver 网络异常重连等场景，大大降低了 client 重建 Watch 的开销，减少了 relist expensive request。

### 更高效的 Watch 恢复机制
虽然 Kubernetes 社区通过 Watch bookmark 机制缓解了 client 与 kube-apiserver 重连等场景下可能导致的 relist expensive request 操作，然而在 kube-apiserver 重启、滚动更新时，它依然还是有可能导致大量的 relist 操作，这是为什么呢？ 如何进一步减少 kube-apiserver 重启场景下的 List 操作呢？如下图所示，在 kube-apiserver 重启后，kubelet 等 client 会立刻带上 Resource Version 发起重建 Watch 的请求。问题就在 kube-apiserver 重启后，watchCache 中的 cyclic buffer 是空的，此时 watchCache 中的最小 Resource Version(listResourceVersion) 是 etcd 的最新全局版本号，也就是图中的 RV200。

![img](https://static001.geekbang.org/resource/image/e1/d2/e1694c3dce75b310b9950f3e3yydd2d2.png?wh=1920*1046)

在不少场景下，client 请求重建 Watch 的 Resource Version 是可能小于 listResourceVersion 的。比如在上面的这个案例图中，集群内 Pod 稳定运行未发生变化，kubelet 假设收到了最新的 RV100 事件。然而这个集群其他资源如 ConfigMap，被管理员不断的修改，它就会导致导致 etcd 版本号新增，ConfigMap 滑动窗口也会不断存储变更事件，从图中可以看到，它记录最大版本号为 RV200。因此 kube-apiserver 重启后，client 请求重建 Pod Watch 的 Resource Version 是 RV100，而 Pod watchCache 中的滑动窗口最小 Resource Version 是 RV200。很显然，RV100 不在 Pod watchCache 所维护的滑动窗口中，kube-apiserver 就会返回"too old resource version"错误给 client，client 只能发起 relist expensive request 操作同步最新数据。为了进一步降低 kube-apiserver 重启对 client Watch 中断的影响，Kubernetes 在 1.20 版本中又进一步实现了更高效的 Watch 恢复机制。它通过 etcd Watch 机制的 Notify 特性，实现了将 etcd 最新的版本号定时推送给 kube-apiserver。kube-apiserver 在将其转换成 ResourceVersion 后，再通过 bookmark 机制推送给 client，避免了 kube-apiserver 重启后 client 可能发起的 List 操作。


### 如何控制 db size
分析完 Kubernetes 如何减少 expensive request，我们再看看 Kubernetes 是如何控制 db size 的。首先，我们知道 Kubernetes 的 kubelet 组件会每隔 10 秒上报一次心跳给 kube-apiserver。其次，Node 资源对象因为包含若干个镜像、数据卷等信息，导致 Node 资源对象会较大，一次心跳消息可能高达 15KB 以上。最后，etcd 是基于 COW(Copy-on-write) 机制实现的 MVCC 数据库，每次修改都会产生新的 key-value，若大量写入会导致 db size 持续增长。早期 Kubernetes 集群由于以上原因，当节点数成千上万时，kubelet 产生的大量写请求就较容易造成 db 大小达到配额，无法写入。

那么如何解决呢？本质上还是 Node 资源对象大的问题。实际上我们需要更新的仅仅是 Node 资源对象的心跳状态，而在 etcd 中我们存储的是整个 Node 资源对象，并未将心跳状态拆分出来。因此 Kuberentes 的解决方案就是将 Node 资源进行拆分，把心跳状态信息从 Node 对象中剥离出来，通过下面的 Lease 对象来描述它。

```
// Lease defines a lease concept.
type Lease struct {
   metav1.TypeMeta `json:",inline"`
   metav1.ObjectMeta `json:"metadata,omitempty" protobuf:"bytes,1,opt,name=metadata"`
   Spec LeaseSpec `json:"spec,omitempty" protobuf:"bytes,2,opt,name=spec"`
}

// LeaseSpec is a specification of a Lease.
type LeaseSpec struct {
   HolderIdentity *string `json:"holderIdentity,omitempty" protobuf:"bytes,1,opt,name=holderIdentity"`
   LeaseDurationSeconds *int32 `json:"leaseDurationSeconds,omitempty" protobuf:"varint,2,opt,name=leaseDurationSeconds"`
   AcquireTime *metav1.MicroTime `json:"acquireTime,omitempty" protobuf:"bytes,3,opt,name=acquireTime"`
   RenewTime *metav1.MicroTime `json:"renewTime,omitempty" protobuf:"bytes,4,opt,name=renewTime"`
   LeaseTransitions *int32 `json:"leaseTransitions,omitempty" protobuf:"varint,5,opt,name=leaseTransitions"`
}
```
因为 Lease 对象非常小，更新的代价远小于 Node 对象，所以这样显著降低了 kube-apiserver 的 CPU 开销、etcd db size，Kubernetes 1.14 版本后已经默认启用 Node 心跳切换到 Lease API。

### 如何优化 key-value 大小
最后，我们再看看 Kubernetes 是如何解决 etcd key-value 大小限制的。在成千上万个节点的集群中，一个服务可能背后有上万个 Pod。而服务对应的 Endpoints 资源含有大量的独立的 endpoints 信息，这会导致 Endpoints 资源大小达到 etcd 的 value 大小限制，etcd 拒绝更新。另外，kube-proxy 等组件会实时监听 Endpoints 资源，一个 endpoint 变化就会产生较大的流量，导致 kube-apiserver 等组件流量超大、出现一系列性能瓶颈。

如何解决以上 Endpoints 资源过大的问题呢？答案依然是拆分、化大为小。Kubernetes 社区设计了 EndpointSlice 概念，每个 EndpointSlice 最大支持保存 100 个 endpoints，成功解决了 key-value 过大、变更同步导致流量超大等一系列瓶颈。

### etcd 优化
Kubernetes 社区在解决大集群的挑战的同时，etcd 社区也在不断优化、新增特性，提升 etcd 在 Kubernetes 场景下的稳定性和性能。这里我简单列举两个，一个是 etcd 并发读特性，一个是 Watch 特性的 Notify 机制。


### 并发读特性
通过以上介绍的各种机制、策略，虽然 Kubernetes 能大大缓解 expensive read request 问题，但是它并不是从本质上来解决问题的。为什么 etcd 无法支持大量的 read expensive request 呢？

除了我们一直强调的容易导致 OOM、大流量导致丢包外，etcd 根本性瓶颈是在 etcd 3.4 版本之前，expensive read request 会长时间持有 MVCC 模块的 buffer 读锁 RLock。而写请求执行完后，需升级锁至 Lock，expensive request 导致写事务阻塞在升级锁过程中，最终导致写请求超时。为了解决此问题，etcd 3.4 版本实现了并发读特性。核心解决方案是去掉了读写锁，每个读事务拥有一个 buffer。在收到读请求创建读事务对象时，全量拷贝写事务维护的 buffer 到读事务 buffer 中。通过并发读特性，显著降低了 List Pod 和 CRD 等 expensive read request 对写性能的影响，延时不再突增、抖动。


### 改善 Watch Notify 机制
为了配合 Kubernetes 社区实现更高效的 Watch 恢复机制，etcd 改善了 Watch Notify 机制，早期 Notify 消息发送间隔是固定的 10 分钟。在 etcd 3.4.11 版本中，新增了 --experimental-watch-progress-notify-interval 参数使 Notify 间隔时间可配置，最小支持为 100ms，满足了 Kubernetes 业务场景的诉求。最后，你要注意的是，默认通过 clientv3 Watch API 创建的 watcher 是不会开启此特性的。你需要创建 Watcher 的时候，设置 clientv3.WithProgressNotify 选项，这样 etcd server 就会定时发送提醒消息给 client，消息中就会携带 etcd 当前最新的全局版本号。


### 小结
最后我们来小结下今天的内容。首先我和你剖析了大集群核心问题，即 expensive request、db size、key-value 大小。针对 expensive request，我分别为你阐述了 Kubernetes 的分页机制、资源按 namespace 拆分部署策略、核心的 Informer 机制、优化 client 与 kube-apiserver 连接异常后的 Watch 恢复效率的 bookmark 机制、以及进一步优化 kube-apiserver 重建场景下 Watch 恢复效率的 Notify 机制。从这个问题优化思路中我们可以看到，优化无止境。从大方向到边界问题，Kubernetes 社区一步步将 expensive request 降低到极致。针对 db size 和 key-value 大小，Kubernetes 社区的解决方案核心思想是拆分，通过 Lease 和 EndpointSlice 资源对象成功解决了大规模集群过程遇到 db size 和 key-value 瓶颈。最后 etcd 社区也在努力提升、优化相关特性，etcd 3.4 版本中的并发读特性和可配置化的 Watch Notify 间隔时间就是最典型的案例。自从 etcd 被 redhat 捐赠给 CNCF 后，etcd 核心就围绕着 Kubernetes 社区展开工作，努力打造更快、更稳的 etcd。


## 21 | 分布式锁：为什么基于etcd实现分布式锁比Redis锁更安全？

在软件开发过程中，我们经常会遇到各种场景要求对共享资源进行互斥操作，否则整个系统的数据一致性就会出现问题。典型场景如商品库存操作、Kubernertes 调度器为 Pod 分配运行的 Node。那要如何实现对共享资源进行互斥操作呢？锁就是其中一个非常通用的解决方案。在单节点多线程环境，你使用本地的互斥锁就可以完成资源的互斥操作。然而单节点存在单点故障，为了保证服务高可用，你需要多节点部署。在多节点部署的分布式架构中，你就需要使用分布式锁来解决资源互斥操作了。但是为什么有的业务使用了分布式锁还会出现各种严重超卖事故呢？分布式锁的实现和使用过程需要注意什么？今天，我就和你聊聊分布式锁背后的故事，我将通过一个茅台超卖的案例，为你介绍基于 Redis 实现的分布锁优缺点，引出分布式锁的核心要素，对比分布式锁的几种业界典型实现方案，深入剖析 etcd 分布式锁的实现。希望通过这节课，让你了解 etcd 分布式锁的应用场景、核心原理，在业务开发过程中，优雅、合理的使用分布式锁去解决各类资源互斥、并发操作问题。


### 从茅台超卖案例看分布式锁要素
首先我们从去年一个因 Redis 分布式锁实现问题导致[茅台超卖案例](https://juejin.cn/post/6854573212831842311)说起，在这个网友分享的真实案例中，因茅台的稀缺性，事件最终定级为 P0 级生产事故，后果影响严重。那么它是如何导致超卖的呢？首先和你简单介绍下此案例中的 Redis 简易分布式锁实现方案，它使用了 Redis SET 命令来实现。

```
SET key value [EX seconds|PX milliseconds|EXAT timestamp|PXAT milliseconds-timestamp|KEEPTTL] [NX|XX] 
[GET]
```
简单给你介绍下 SET 命令重点参数含义：EX  设置过期时间，单位秒；NX 当 key 不存在的时候，才设置 key；XX 当 key 存在的时候，才设置 key。


此业务就是基于 Set key value EX 10 NX 命令来实现的分布式锁，并通过 JAVA 的 try-finally 语句，执行 Del key 语句来释放锁，简易流程如下：

```
# 对资源key加锁，key不存在时创建，并且设置，10秒自动过期
SET key value EX 10 NX
业务逻辑流程1，校验用户身份
业务逻辑流程2，查询并校验库存(get and compare)
业务逻辑流程3，库存>0，扣减库存(Decr stock)，生成秒杀茅台订单

# 释放锁
Del key
```
以上流程中其实存在以下思考点:NX 参数有什么作用?为什么需要原子的设置 key 及过期时间？为什么基于 Set key value EX 10 NX 命令还出现了超卖呢?为什么大家都比较喜欢使用 Redis 作为分布式锁实现？

首先来看第一个问题，NX 参数的作用。NX 参数是为了保证当分布式锁不存在时，只有一个 client 能写入此 key 成功，获取到此锁。我们使用分布式锁的目的就是希望在高并发系统中，有一种互斥机制来防止彼此相互干扰，保证数据的一致性。

**因此分布式锁的第一核心要素就是互斥性、安全性。在同一时间内，不允许多个 client 同时获得锁。**

再看第二个问题，假设我们未设置 key 自动过期时间，在 Set key value NX 后，如果程序 crash 或者发生网络分区后无法与 Redis 节点通信，毫无疑问其他 client 将永远无法获得锁。这将导致死锁，服务出现中断。有的同学意识到这个问题后，使用如下 SETNX 和 EXPIRE 命令去设置 key 和过期时间，这也是不正确的，因为你无法保证 SETNX 和 EXPIRE 命令的原子性。

```
# 对资源key加锁，key不存在时创建
SETNX key value
# 设置KEY过期时间
EXPIRE key 10
业务逻辑流程

# 释放锁
Del key
```
**这就是分布式锁第二个核心要素，活性。在实现分布式锁的过程中要考虑到 client 可能会出现 crash 或者网络分区，你需要原子申请分布式锁及设置锁的自动过期时间，通过过期、超时等机制自动释放锁，避免出现死锁，导致业务中断。**


再看第三个问题，为什么使用了 Set key value EX 10 NX 命令，还出现了超卖呢？原来是抢购活动开始后，加锁逻辑中的业务流程 1 访问的用户身份服务出现了高负载，导致阻塞在校验用户身份流程中 (超时 30 秒)，然而锁 10 秒后就自动过期了，因此其他 client 能获取到锁。关键是阻塞的请求执行完后，它又把其他 client 的锁释放掉了，导致进入一个恶性循环。因此申请锁时，写入的 value 应确保唯一性（随机值等）。client 在释放锁时，应通过 Lua 脚本原子校验此锁的 value 与自己写入的 value 一致，若一致才能执行释放工作。更关键的是库存校验是通过 get and compare 方式，它压根就无法防止超卖。正确的解决方案应该是通过 LUA 脚本实现 Redis 比较库存、扣减库存操作的原子性（或者在每次只能抢购一个的情况下，通过判断[Redis Decr](https://redis.io/commands/DECR) 命令的返回值即可。此命令会返回扣减后的最新库存，若小于 0 则表示超卖）。

**从这个问题中我们可以看到，分布式锁实现具备一定的复杂度，它不仅依赖存储服务提供的核心机制，同时依赖业务领域的实现。无论是遭遇高负载、还是宕机、网络分区等故障，都需确保锁的互斥性、安全性，否则就会出现严重的超卖生产事故。**

再看最后一个问题，为什么大家都比较喜欢使用 Redis 做分布式锁的实现呢?考虑到在秒杀等业务场景上存在大量的瞬间、高并发请求，加锁与释放锁的过程应是高性能、高可用的。而 Redis 核心优点就是快、简单，是随处可见的基础设施，部署、使用也及其方便，因此广受开发者欢迎。

**这就是分布式锁第三个核心要素，高性能、高可用。加锁、释放锁的过程性能开销要尽量低，同时要保证高可用，确保业务不会出现中断。**

那么除了以上案例中人为实现问题导致的锁不安全因素外，基于 Redis 实现的以上分布式锁还有哪些安全性问题呢？


### Redis 分布式锁问题
我们从茅台超卖案例中为你总结出的分布式核心要素（互斥性、安全性、活性、高可用、高性能）说起。首先，如果我们的分布式锁跑在单节点的 Redis Master 节点上，那么它就存在单点故障，无法保证分布式锁的高可用。于是我们需要一个主备版的 Redis 服务，至少具备一个 Slave 节点。

我们又知道 Redis 是基于主备异步复制协议实现的 Master-Slave 数据同步，如下图所示，若 client A 执行 SET key value EX 10 NX 命令，redis-server 返回给 client A 成功后，Redis Master 节点突然出现 crash 等异常，这时候 Redis Slave 节点还未收到此命令的同步。

![img](https://static001.geekbang.org/resource/image/cd/45/cd3d4ab1af45c6eb76e7dccd9c666245.png?wh=1920*994)

若你部署了 Redis Sentinel 等主备切换服务，那么它就会以 Slave 节点提升为主，此时 Slave 节点因并未执行 SET key value EX 10 NX 命令，因此它收到 client B 发起的加锁的此命令后，它也会返回成功给 client。那么在同一时刻，集群就出现了两个 client 同时获得锁，分布式锁的互斥性、安全性就被破坏了。除了主备切换可能会导致基于 Redis 实现的分布式锁出现安全性问题，在发生网络分区等场景下也可能会导致出现脑裂，Redis 集群出现多个 Master，进而也会导致多个 client 同时获得锁。如下图所示，Master 节点在可用区 1，Slave 节点在可用区 2，当可用区 1 和可用区 2 发生网络分区后，部署在可用区 2 的 Redis Sentinel 服务就会将可用区 2 的 Slave 提升为 Master，而此时可用区 1 的 Master 也在对外提供服务。因此集群就出现了脑裂，出现了两个 Master，都可对外提供分布式锁申请与释放服务，分布式锁的互斥性被严重破坏。

![img](https://static001.geekbang.org/resource/image/cb/b1/cb4cb52cf2244d2000884ef5f5ff3db1.png?wh=1920*1026)

**主备切换、脑裂是 Redis 分布式锁的两个典型不安全的因素，本质原因是 Redis 为了满足高性能，采用了主备异步复制协议，同时也与负责主备切换的 Redis Sentinel 服务是否合理部署有关。**

有没有其他方案解决呢？当然有，Redis 作者为了解决 SET key value [EX] 10 [NX]命令实现分布式锁不安全的问题，提出了[RedLock 算法](https://redis.io/topics/distlock/)。它是基于多个独立的 Redis Master 节点的一种实现（一般为 5）。client 依次向各个节点申请锁，若能从多数个节点中申请锁成功并满足一些条件限制，那么 client 就能获取锁成功。它通过独立的 N 个 Master 节点，避免了使用主备异步复制协议的缺陷，只要多数 Redis 节点正常就能正常工作，显著提升了分布式锁的安全性、可用性。但是，它的实现建立在一个不安全的系统模型上的，它依赖系统时间，当时钟发生跳跃时，也可能会出现安全性问题。你要有兴趣的话，可以详细阅读下分布式存储专家 Martin 对[RedLock 的分析文章](https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html)，Redis 作者的也专门写了[一篇文章进行了反驳](http://antirez.com/news/101)。


### 分布式锁常见实现方案
了解完 Redis 分布式锁的一系列问题和实现方案后，我们再看看还有哪些典型的分布式锁实现。除了 Redis 分布式锁，其他使用最广的应该是 ZooKeeper 分布式锁和 etcd 分布式锁。ZooKeeper 也是一个典型的分布式元数据存储服务，它的分布式锁实现基于 ZooKeeper 的临时节点和顺序特性。首先什么是临时节点呢？临时节点具备数据自动删除的功能。当 client 与 ZooKeeper 连接和 session 断掉时，相应的临时节点就会被删除。其次 ZooKeeper 也提供了 Watch 特性可监听 key 的数据变化。

[使用 Zookeeper 加锁的伪代码如下：](https://www.usenix.org/legacy/event/atc10/tech/full_papers/Hunt.pdf)

```
Lock
1 n = create(l + “/lock-”, EPHEMERAL|SEQUENTIAL)
2 C = getChildren(l, false)
3 if n is lowest znode in C, exit
4 p = znode in C ordered just before n
5 if exists(p, true) wait for watch event
6 goto 2
Unlock
1 delete(n)
```
接下来我重点给你介绍一下基于 etcd 的分布式锁实现。


### etcd 分布式锁实现
那么基于 etcd 实现的分布式锁是如何确保安全性、互斥性、活性的呢？

### 事务与锁的安全性
从 Redis 案例中我们可以看到，加锁的过程需要确保安全性、互斥性。比如，当 key 不存在时才能创建，否则查询相关 key 信息，而 etcd 提供的事务能力正好可以满足我们的诉求。正如我在09中给你介绍的事务特性，它由 IF 语句、Then 语句、Else 语句组成。其中在 IF 语句中，支持比较 key 的是修改版本号 mod_revision 和创建版本号 create_revision。在分布式锁场景，你就可以通过 key 的创建版本号 create_revision 来检查 key 是否已存在，因为一个 key 不存在的话，它的 create_revision 版本号就是 0。若 create_revision 是 0，你就可发起 put 操作创建相关 key，具体代码如下:


```
txn := client.Txn(ctx).If(v3.Compare(v3.CreateRevision(k), 
"=", 0))
```
你要注意的是，实现分布式锁的方案有多种，比如你可以通过 client 是否成功创建一个固定的 key，来判断此 client 是否获得锁，你也可以通过多个 client 创建 prefix 相同，名称不一样的 key，哪个 key 的 revision 最小，最终就是它获得锁。至于谁优谁劣，我作为思考题的一部分，留给大家一起讨论。相比 Redis 基于主备异步复制导致锁的安全性问题，etcd 是基于 Raft 共识算法实现的，一个写请求需要经过集群多数节点确认。因此一旦分布式锁申请返回给 client 成功后，它一定是持久化到了集群多数节点上，不会出现 Redis 主备异步复制可能导致丢数据的问题，具备更高的安全性。


### Lease 与锁的活性
通过事务实现原子的检查 key 是否存在、创建 key 后，我们确保了分布式锁的安全性、互斥性。那么 etcd 是如何确保锁的活性呢? 也就是发生任何故障，都可避免出现死锁呢？正如在06租约特性中和你介绍的，Lease 就是一种活性检测机制，它提供了检测各个客户端存活的能力。你的业务 client 需定期向 etcd 服务发送"特殊心跳"汇报健康状态，若你未正常发送心跳，并超过和 etcd 服务约定的最大存活时间后，就会被 etcd 服务移除此 Lease 和其关联的数据。通过 Lease 机制就优雅地解决了 client 出现 crash 故障、client 与 etcd 集群网络出现隔离等各类故障场景下的死锁问题。一旦超过 Lease TTL，它就能自动被释放，确保了其他 client 在 TTL 过期后能正常申请锁，保障了业务的可用性。具体代码如下:


```
txn := client.Txn(ctx).If(v3.Compare(v3.CreateRevision(k), "=", 0))
txn = txn.Then(v3.OpPut(k, val, v3.WithLease(s.Lease())))
txn = txn.Else(v3.OpGet(k))
resp, err := txn.Commit()
if err != nil {
    return err
}
```
### Watch 与锁的可用性
当一个持有锁的 client crash 故障后，其他 client 如何快速感知到此锁失效了，快速获得锁呢，最大程度降低锁的不可用时间呢？答案是 Watch 特性。正如在 08 Watch 特性中和你介绍的，Watch 提供了高效的数据监听能力。当其他 client 收到 Watch Delete 事件后，就可快速判断自己是否有资格获得锁，极大减少了锁的不可用时间。具体代码如下所示：

```
var wr v3.WatchResponse
wch := client.Watch(cctx, key, v3.WithRev(rev))
for wr = range wch {
   for _, ev := range wr.Events {
      if ev.Type == mvccpb.DELETE {
         return nil
      }
   }
}
```
### etcd 自带的 concurrency 包
为了帮助你简化分布式锁、分布式选举、分布式事务的实现，etcd 社区提供了一个名为 concurrency 包帮助你更简单、正确地使用分布式锁、分布式选举。下面我简单为你介绍下分布式锁concurrency包的使用和实现，它的使用非常简单，如下代码所示，核心流程如下：

首先通过 concurrency.NewSession 方法创建 Session，本质是创建了一个 TTL 为 10 的 Lease。其次得到 session 对象后，通过 concurrency.NewMutex 创建了一个 mutex 对象，包含 Lease、key prefix 等信息。然后通过 mutex 对象的 Lock 方法尝试获取锁。最后使用结束，可通过 mutex 对象的 Unlock 方法释放锁。

```
cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
if err != nil {
   log.Fatal(err)
}
defer cli.Close()
// create two separate sessions for lock competition
s1, err := concurrency.NewSession(cli, concurrency.WithTTL(10))
if err != nil {
   log.Fatal(err)
}
defer s1.Close()
m1 := concurrency.NewMutex(s1, "/my-lock/")
// acquire lock for s1
if err := m1.Lock(context.TODO()); err != nil {
   log.Fatal(err)
}
fmt.Println("acquired lock for s1")
if err := m1.Unlock(context.TODO()); err != nil {
   log.Fatal(err)
}
fmt.Println("released lock for s1")
```

那么 mutex 对象的 Lock 方法是如何加锁的呢？核心还是使用了我们上面介绍的事务和 Lease 特性，当 CreateRevision 为 0 时，它会创建一个 prefix 为 /my-lock 的 key（ /my-lock + LeaseID)，并获取到 /my-lock prefix 下面最早创建的一个 key（revision 最小），分布式锁最终是由写入此 key 的 client 获得，其他 client 则进入等待模式。详细代码如下：


```
m.myKey = fmt.Sprintf("%s%x", m.pfx, s.Lease())
cmp := v3.Compare(v3.CreateRevision(m.myKey), "=", 0)
// put self in lock waiters via myKey; oldest waiter holds lock
put := v3.OpPut(m.myKey, "", v3.WithLease(s.Lease()))
// reuse key in case this session already holds the lock
get := v3.OpGet(m.myKey)
// fetch current holder to complete uncontended path with only one RPC
getOwner := v3.OpGet(m.pfx, v3.WithFirstCreate()...)
resp, err := client.Txn(ctx).If(cmp).Then(put, getOwner).Else(get, getOwner).Commit()
if err != nil {
   return err
}
```

那未获得锁的 client 是如何等待的呢?答案是通过 Watch 机制各自监听 prefix 相同，revision 比自己小的 key，因为只有 revision 比自己小的 key 释放锁，我才能有机会，获得锁，如下代码所示，其中 waitDelete 会使用我们上面的介绍的 Watch 去监听比自己小的 key，详细代码可参考[concurrency mutex](https://github.com/etcd-io/etcd/blob/v3.4.9/clientv3/concurrency/mutex.go)的实现。

```
// wait for deletion revisions prior to myKey
hdr, werr := waitDeletes(ctx, client, m.pfx, m.myRev-1)
// release lock key if wait failed
if werr != nil {
   m.Unlock(client.Ctx())
} else {
   m.hdr = hdr
}
```

### 小结
最后我们来小结下今天的内容。今天我通过一个 Redis 分布式锁实现问题——茅台超卖案例，给你介绍了分布式锁的三个主要核心要素，它们分别如下：安全性、互斥性。在同一时间内，不允许多个 client 同时获得锁。活性。无论 client 出现 crash 还是遭遇网络分区，你都需要确保任意故障场景下，都不会出现死锁，常用的解决方案是超时和自动过期机制。高可用、高性能。加锁、释放锁的过程性能开销要尽量低，同时要保证高可用，避免单点故障。

随后我通过这个案例，继续和你分析了 Redis SET 命令创建分布式锁的安全性问题。单 Redis Master 节点存在单点故障，一主多备 Redis 实例又因为 Redis 主备异步复制，当 Master 节点发生 crash 时，可能会导致同时多个 client 持有分布式锁，违反了锁的安全性问题。为了优化以上问题，Redis 作者提出了 RedLock 分布式锁，它基于多个独立的 Redis Master 节点工作，只要一半以上节点存活就能正常工作，同时不依赖 Redis 主备异步复制，具有良好的安全性、高可用性。然而它的实现依赖于系统时间，当发生时钟跳变的时候，也会出现安全性问题。最后我和你重点介绍了 etcd 的分布式锁实现过程中的一些技术点。它通过 etcd 事务机制，校验 CreateRevision 为 0 才能写入相关 key。若多个 client 同时申请锁，则 client 通过比较各个 key 的 revision 大小，判断是否获得锁，确保了锁的安全性、互斥性。通过 Lease 机制确保了锁的活性，无论 client 发生 crash 还是网络分区，都能保证不会出现死锁。通过 Watch 机制使其他 client 能快速感知到原 client 持有的锁已释放，提升了锁的可用性。最重要的是 etcd 是基于 Raft 协议实现的高可靠、强一致存储，正常情况下，不存在 Redis 主备异步复制协议导致的数据丢失问题。


## 22 | 配置及服务发现：解析etcd在API Gateway开源项目中应用

在软件开发的过程中，为了提升代码的灵活性和开发效率，我们大量使用配置去控制程序的运行行为。从简单的数据库账号密码配置，到[confd](https://github.com/kelseyhightower/confd)支持以 etcd 为后端存储的本地配置及模板管理，再到[Apache APISIX](https://github.com/apache/apisix)等 API Gateway 项目使用 etcd 存储服务配置、路由信息等，最后到 Kubernetes 更实现了 Secret 和 ConfigMap 资源对象来解决配置管理的问题。那么它们是如何实现实时、动态调整服务配置而不需要重启相关服务的呢？今天我就和你聊聊 etcd 在配置和服务发现场景中的应用。我将以开源项目 Apache APISIX 为例，为你分析服务发现的原理，带你了解 etcd 的 key-value 模型，Watch 机制，鉴权机制，Lease 特性，事务特性在其中的应用。希望通过这节课，让你了解 etcd 在配置系统和服务发现场景工作原理，帮助你选型适合业务场景的配置系统、服务发现组件。同时，在使用 Apache APISIX 等开源项目过程中遇到 etcd 相关问题时，你能独立排查、分析，并向社区提交 issue 和 PR 解决。


### 服务发现
首先和你聊聊服务发现，服务发现是指什么？为什么需要它呢?为了搞懂这个问题，我首先和你分享下程序部署架构的演进。

### 单体架构
在早期软件开发时使用的是单体架构，也就是所有功能耦合在同一个项目中，统一构建、测试、发布。单体架构在项目刚启动的时候，架构简单、开发效率高，比较容易部署、测试。但是随着项目不断增大，它具有若干缺点，比如：所有功能耦合在同一个项目中，修复一个小 Bug 就需要发布整个大工程项目，增大引入问题风险。同时随着开发人员增多、单体项目的代码增长、各模块堆砌在一起、代码质量参差不齐，内部复杂度会越来越高，可维护性差。无法按需针对仅出现瓶颈的功能模块进行弹性扩容，只能作为一个整体继续扩展，因此扩展性较差。一旦单体应用宕机，将导致所有服务不可用，因此可用性较差。

### 分布式及微服务架构
如何解决以上痛点呢？当然是将单体应用进行拆分，大而化小。如何拆分呢？ 这里我就以一个我曾经参与重构建设的电商系统为案例给你分析一下。在一个单体架构中，完整的电商系统应包括如下模块：商城系统，负责用户登录、查看及搜索商品、购物车商品管理、优惠券管理、订单管理、支付等功能。物流及仓储系统，根据用户订单，进行发货、退货、换货等一系列仓储、物流管理。其他客服系统、客户管理系统等。

因此在分布式架构中，你可以按整体功能，将单体应用垂直拆分成以上三大功能模块，各个功能模块可以选择不同的技术栈实现，按需弹性扩缩容，如下图所示。

![img](https://static001.geekbang.org/resource/image/ca/20/ca6090e229dde9a0361d6yy2c3df8d20.png?wh=1920*1046)

那什么又是微服务架构呢？它是对各个功能模块进行更细立度的拆分，比如商城系统模块可以拆分成：

用户鉴权模块；商品模块；购物车模块；优惠券模块；支付模块；……

在微服务架构中，每个模块职责更单一、独立部署、开发迭代快，如下图所示。

![img](https://static001.geekbang.org/resource/image/cf/4a/cf62b7704446c05d8747b4672b5fb74a.png?wh=1920*1048)

那么在分布式及微服务架构中，各个模块之间如何及时知道对方网络地址与端口、协议，进行接口调用呢？

### 为什么需要服务发现中间件?
其实这个知道的过程，就是服务发现。在早期的时候我们往往通过硬编码、配置文件声明各个依赖模块的网络地址、端口，然而这种方式在分布式及微服务架构中，其运维效率、服务可用性是远远不够的。那么我们能否实现通过一个特殊服务就查询到各个服务的后端部署地址呢？ 各服务启动的时候，就自动将 IP 和 Port、协议等信息注册到特殊服务上，当某服务出现异常的时候，特殊服务就自动删除异常实例信息？是的，当然可以，这个特殊服务就是注册中心服务，你可以基于 etcd、ZooKeeper、consul 等实现。

### etcd 服务发现原理
那么如何基于 etcd 实现服务发现呢?下面我给出了一个通用的服务发现原理架构图，通过此图，为你介绍下服务发现的基本原理。详细如下：

整体上分为四层，client 层、proxy 层 (可选)、业务 server、etcd 存储层组成。引入 proxy 层的原因是使 client 更轻、逻辑更简单，无需直接访问存储层，同时可通过 proxy 层支持各种协议。client 层通过负载均衡访问 proxy 组件。proxy 组件启动的时候，通过 etcd 的 Range RPC 方法从 etcd 读取初始化服务配置数据，随后通过 Watch 接口持续监听后端业务 server 扩缩容变化，实时修改路由。proxy 组件收到 client 的请求后，它根据从 etcd 读取到的对应服务的路由配置、负载均衡算法（比如 Round-robin）转发到对应的业务 server。业务 server 启动的时候，通过 etcd 的写接口 Txn/Put 等，注册自身地址信息、协议到高可用的 etcd 集群上。业务 server 缩容、故障时，对应的 key 应能自动从 etcd 集群删除，因此相关 key 需要关联 lease 信息，设置一个合理的 TTL，并定时发送 keepalive 请求给 Leader 续租，以防止租约及 key 被淘汰。

![img](https://static001.geekbang.org/resource/image/26/e4/26d0d18c0725de278eeb7505f20642e4.png?wh=1920*1137)

当然，在分布式及微服务架构中，我们面对的问题不仅仅是服务发现，还包括如下痛点：限速；鉴权；安全；日志；监控；丰富的发布策略；链路追踪；......

为了解决以上痛点，各大公司及社区开发者推出了大量的开源项目。这里我就以国内开发者广泛使用的 Apache APISIX 项目为例，为你分析 etcd 在其中的应用，了解下它是怎么玩转服务发现的。



### Apache APISIX 原理
Apache APISIX 它具备哪些功能呢？它的本质是一个无状态、高性能、实时、动态、可水平扩展的 API 网关。核心原理就是基于你配置的服务信息、路由规则等信息，将收到的请求通过一系列规则后，正确转发给后端的服务。Apache APISIX 其实就是上面服务发现原理架构图中的 proxy 组件，如下图红色虚线框所示。

![img](https://static001.geekbang.org/resource/image/20/fd/20a539bdd37db2d4632c7b0c5f4119fd.png?wh=1920*1246)

Apache APISIX 详细架构图如下（[引用自社区项目文档](https://github.com/apache/apisix)）。从图中你可以看到，它由控制面和数据面组成。控制面顾名思义，就是你通过 Admin API 下发服务、路由、安全配置的操作。控制面默认的服务发现存储是 etcd，当然也支持 consul、nacos 等。你如果没有使用过 Apache APISIX 的话，可以参考下这个[example](https://github.com/apache/apisix-docker/tree/master/example)，快速、直观的了解下 Apache APISIX 是如何通过 Admin API 下发服务和路由配置的。数据面是在实现基于服务路由信息数据转发的基础上，提供了限速、鉴权、安全、日志等一系列功能，也就是解决了我们上面提的分布式及微服务架构中的典型痛点。

![img](https://static001.geekbang.org/resource/image/83/f4/834502c6ed7e59fe0b4643c11b2d31f4.png?wh=1746*838)

那么当我们通过控制面 API 新增一个服务时，Apache APISIX 是是如何实现实时、动态调整服务配置，而不需要重启网关服务的呢？下面，我就和你聊聊 etcd 在 Apache APISIX 项目中的应用。

### etcd 在 Apache APISIX 中的应用
在搞懂这个问题之前，我们先看看 Apache APISIX 在 etcd 中，都存储了哪些数据呢？它的数据存储格式是怎样的？


#### 数据存储格式
下面我参考 Apache APISIX 的example案例（apisix:2.3），通过 Admin API 新增了两个服务、路由规则后，执行如下查看 etcd 所有 key 的命令：

```
etcdctl get "" --prefix --keys-only
```
etcd 输出结果如下：

```
/apisix/consumers/
/apisix/data_plane/server_info/f7285805-73e9-4ce4-acc6-a38d619afdc3
/apisix/global_rules/
/apisix/node_status/
/apisix/plugin_metadata/
/apisix/plugins
/apisix/plugins/
/apisix/proto/
/apisix/routes/
/apisix/routes/12
/apisix/routes/22
/apisix/services/
/apisix/services/1
/apisix/services/2
/apisix/ssl/
/apisix/ssl/1
/apisix/ssl/2
/apisix/stream_routes/
/apisix/upstreams/
```
然后我们继续通过 etcdctl get 命令查看下 services 都存储了哪些信息呢？

```
root@e9d3b477ca1f:/opt/bitnami/etcd# etcdctl get /apisix/services --prefix
/apisix/services/
init_dir
/apisix/services/1
{"update_time":1614293352,"create_time":1614293352,"upstream":{"type":"roundrobin","nodes":{"172.18.5.12:80":1},"hash_on":"vars","scheme":"http","pass_host":"pass"},"id":"1"}
/apisix/services/2
{"update_time":1614293361,"create_time":1614293361,"upstream":
{"type":"roundrobin","nodes":{"172.18.5.13:80":1},"hash_on":"vars","scheme":"http","pass_host":"pass"},"id":"2"}
```

从中我们可以总结出如下信息：Apache APSIX 2.x 系列版本使用的是 etcd3。服务、路由、ssl、插件等配置存储格式前缀是 /apisix + "/" + 功能特性类型（routes/services/ssl 等），我们通过 Admin API 添加的路由、服务等配置就保存在相应的前缀下。路由和服务配置的 value 是个 Json 对象，其中服务对象包含了 id、负载均衡算法、后端节点、协议等信息。

了解完 Apache APISIX 在 etcd 中的数据存储格式后，那么它是如何动态、近乎实时地感知到服务配置变化的呢？

### Watch 机制的应用
与 Kubernetes 一样，它们都是通过 etcd 的 Watch 机制来实现的。Apache APISIX 在启动的时候，首先会通过 Range 操作获取网关的配置、路由等信息，随后就通过 Watch 机制，获取增量变化事件。使用 Watch 机制最容易犯错的地方是什么呢？

答案是不处理 Watch 返回的相关错误信息，比如已压缩 ErrCompacted 错误。Apache APISIX 项目在从 etcd v2 中切换到 etcd v3 早期的时候，同样也犯了这个错误。去年某日收到小伙伴求助，说使用 Apache APISIX 后，获取不到新的服务配置了，是不是 etcd 出什么 Bug 了？经过一番交流和查看日志，发现原来是 Apache APISIX 未处理 ErrCompacted 错误导致的。根据我们07Watch 原理的介绍，当你请求 Watch 的版本号已被 etcd 压缩后，etcd 就会取消这个 watcher，这时你需要重建 watcher，才能继续监听到最新数据变化事件。查清楚问题后，小伙伴向社区提交了 issue 反馈，随后 Apache APISIX 相关同学通过[PR 2687](https://github.com/apache/apisix/pull/2687)修复了此问题，更多信息你可参考 Apache APISIX 访问 etcd[相关实现代码文件](https://github.com/apache/apisix/blob/v2.3/apisix/core/etcd.lua)。


### 鉴权机制的应用
除了 Watch 机制，Apache APISIX 项目还使用了鉴权，毕竟配置网关是个高危操作，那它是如何使用 etcd 鉴权机制的呢？ etcd 鉴权机制中最容易踩的坑是什么呢？答案是不复用 client 和鉴权 token，频繁发起 Authenticate 操作，导致 etcd 高负载。正如我在17和你介绍的，一个 8 核 32G 的高配节点在 100 个连接时，Authenticate QPS 仅为 8。可想而知，你如果不复用 token，那么出问题就很自然不过了。Apache APISIX 是否也踩了这个坑呢？Apache APISIX 是基于 Lua 构建的，使用的是[lua-resty-etcd](https://github.com/api7/lua-resty-etcd/blob/master/lib/resty/etcd/v3.lua)这个项目访问 etcd，从相关[issue](https://github.com/apache/apisix/issues/2899)反馈看，的确也踩了这个坑。社区用户反馈后，随后通过复用 client、更完善的 token 复用机制解决了 Authenticate 的性能瓶颈，详细信息你可参考[PR 2932](https://github.com/apache/apisix/pull/2932)、[PR 100](https://github.com/api7/lua-resty-etcd/pull/100)。除了以上介绍的 Watch 机制、鉴权机制，Apache APISIX 还使用了 etcd 的 Lease 特性和事务接口。


### Lease 特性的应用
为什么 Apache APISIX 项目需要 Lease 特性呢？服务发现的核心工作原理是服务启动的时候将地址信息登录到注册中心，服务异常时自动从注册中心删除。这是不是跟我们前面05节介绍的 < Lease 特性: 如何检测客户端的存活性 > 应用场景很匹配呢？没错，Apache APISIX 通过 etcd v2 的 TTL 特性、etcd v3 的 Lease 特性来实现类似的效果，它提供的增加服务路由 API，支持设置 TTL 属性，如下面所示：



```
# Create a route expires after 60 seconds, then it's deleted automatically
$ curl http://127.0.0.1:9080/apisix/admin/routes/2?ttl=60 -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -i -d '
{
    "uri": "/aa/index.html",
    "upstream": {
        "type": "roundrobin",
        "nodes": {
            "39.97.63.215:80": 1
        }
    }
}'
```
当一个路由设置非 0 TTL 后，Apache APISIX 就会为它创建 Lease，关联 key，相关代码如下：

```
-- lease substitute ttl in v3
local res, err
if ttl then
    local data, grant_err = etcd_cli:grant(tonumber(ttl))
    if not data then
        return nil, grant_err
    end
    res, err = etcd_cli:set(prefix .. key, value, {prev_kv = true, lease = data.body.ID})
else
    res, err = etcd_cli:set(prefix .. key, value, {prev_kv = true})
end
```
### 事务特性的应用
介绍完 Lease 特性在 Apache APISIX 项目中的应用后，我们再来思考两个问题。为什么它还依赖 etcd 的事务特性呢？简单的执行 put 接口有什么问题？答案是它跟 Kubernetes 是一样的使用目的。使用事务是为了防止并发场景下的数据写冲突，比如你可能同时发起两个 Patch Admin API 去修改配置等。如果简单地使用 put 接口，就会导致第一个写请求的结果被覆盖。Apache APISIX 是如何使用事务接口提供的乐观锁机制去解决并发冲突的问题呢？核心依然是我们前面课程中一直强调的 mod_revision，它会比较事务提交时的 mod_revision 与预期是否一致，一致才能执行 put 操作，Apache APISIX 相关使用代码如下：

```
local compare = {
    {
        key = key,
        target = "MOD",
        result = "EQUAL",
        mod_revision = mod_revision,
    }
}
local success = {
    {
        requestPut = {
            key = key,
            value = value,
            lease = lease_id,
        }
    }
}
local res, err = etcd_cli:txn(compare, success)
if not res then
    return nil, err
end
```
关于 Apache APISIX 事务特性的引入、背景以及更详细的实现，你也可以参考[PR 2216](https://github.com/apache/apisix/pull/2216)。


### 小结
最后我们来小结下今天的内容。今天我给你介绍了服务部署架构的演进，我们从单体架构的缺陷开始、到分布式及微服务架构的诞生，和你分享了分布式及微服务架构中面临的一系列痛点（如服务发现，鉴权，安全，限速等等）。而开源项目 Apache APISIX 正是一个基于 etcd 的项目，它为后端存储提供了一系列的解决方案，我通过它的架构图为你介绍了其控制面和数据面的工作原理。随后我从数据存储格式、Watch 机制、鉴权机制、Lease 特性以及事务特性维度，和你分析了它们在 Apache APISIX 项目中的应用。数据存储格式上，APISIX 采用典型的 prefix + 功能特性组织格式。key 是相关配置 id，value 是个 json 对象，包含一系列业务所需要的核心数据。你需要注意的是 Apache APISIX 1.x 版本使用的 etcd v2 API，2.x 版本使用的是 etcd v3 API，要求至少是 etcd v3.4 版本以上。Watch 机制上，APISIX 依赖它进行配置的动态、实时更新，避免了传统的修改配置，需要服务重启等缺陷。鉴权机制上，APISIX 使用密码认证，进行多租户认证、授权，防止用户出现越权访问，保护网关服务的安全。Lease 及事务特性上，APISIX 通过 Lease 来设置自动过期的路由规则，解决服务发现中的节点异常自动剔除等问题，通过事务特性的乐观锁机制来实现并发场景下覆盖更新等问题。希望通过本节课的学习，让你从 etcd 角度更深入了解 APISIX 项目的原理，了解 etcd 各个特性在其中的应用，学习它的最佳实践经验和经历的各种坑，避免重复踩坑。在以后的工作中，在你使用 APISIX 等开源项目遇到 etcd 相关错误时，能独立分析、排查，甚至给社区提交 PR 解决。

## 23 | 选型：etcd/ZooKeeper/Consul等我们该如何选择？

在软件开发过程中，当我们需要解决配置、服务发现、分布式锁等业务痛点，在面对etcd、ZooKeeper、Consul、Nacos等一系列候选开源项目时，我们应该如何结合自己的业务场景，选择合适的分布式协调服务呢？今天，我就和你聊聊主要分布式协调服务的对比。我将从基本架构、共识算法、数据模型、重点特性、容灾能力等维度出发，带你了解主要分布式协调服务的基本原理和彼此之间的差异性。希望通过这节课，让你对 etcd、ZooKeeper、Consul 原理和特性有一定的理解，帮助你选型适合业务场景的配置系统、服务发现组件。

### 基本架构及原理
在详细和你介绍对比 etcd、ZooKeeper、Consul 特性之前，我们先从整体架构上来了解一下各开源项目的核心架构及原理。

#### etcd 架构及原理
首先是 etcd，etcd 我们知道它是基于复制状态机实现的分布式协调服务。如下图所示，由 Raft 共识模块、日志模块、基于 boltdb 持久化存储的状态机组成。

![img](https://static001.geekbang.org/resource/image/5c/4f/5c7a3079032f90120a6b309ee401fc4f.png?wh=605*319)



以下是 etcd 基于复制状态机模型的写请求流程：client 发起一个写请求（put x = 3）；etcdserver 模块向 Raft 共识模块提交请求，共识模块生成一个写提案日志条目。若 server 是 Leader，则把日志条目广播给其他节点，并持久化日志条目到 WAL 中；当一半以上节点持久化日志条目后，Leader 的共识模块将此日志条目标记为已提交（committed），并通知其他节点提交；etcdserver 模块从 Raft 共识模块获取已经提交的日志条目，异步应用到 boltdb 状态机存储中，然后返回给 client。

更详细的原理我就不再重复描述，你可以参考02读和03写两节原理介绍。

### ZooKeeper 架构及原理
接下来我和你简要介绍下ZooKeeper原理，下图是它的架构图。


如下面架构图所示，你可以看到 ZooKeeper 中的节点与 etcd 类似，也划分为 Leader 节点、Follower 节点、Observer 节点（对应的 Raft 协议的 Learner 节点）。同时，写请求统一由 Leader 处理，读请求各个节点都能处理。

不一样的是它们的读行为和共识算法。在读行为上，ZooKeeper 默认读可能会返回 stale data，而 etcd 使用的线性读，能确保读取到反应集群共识的最新数据。共识算法上，etcd 使用的是 Raft，ZooKeeper 使用的是 Zab。

![img](https://static001.geekbang.org/resource/image/7a/d3/7a84bcaef9e53ba19d7d88e6ed6504d3.png?wh=1920*1013)

那什么是 Zab 协议呢？Zab 协议可以分为以下阶段：

Phase 0，Leader 选举（Leader Election)。一个节点只要求获得半数以上投票，就可以当选为准 Leader；Phase 1，发现（Discovery）。准 Leader 收集其他节点的数据信息，并将最新的数据复制到自身；Phase 2，同步（Synchronization）。准 Leader 将自身最新数据复制给其他落后的节点，并告知其他节点自己正式当选为 Leader；Phase 3，广播（Broadcast）。Leader 正式对外服务，处理客户端写请求，对消息进行广播。当收到一个写请求后，它会生成 Proposal 广播给各个 Follower 节点，一半以上 Follower 节点应答之后，Leader 再发送 Commit 命令给各个 Follower，告知它们提交相关提案；

ZooKeeper 是如何实现的 Zab 协议的呢？ZooKeeper 在实现中并未严格按[论文](https://marcoserafini.github.io/papers/zab.pdf)定义的分阶段实现，而是对部分阶段进行了整合，分别如下：

Fast Leader Election。首先 ZooKeeper 使用了一个名为 Fast Leader Election 的选举算法，通过 Leader 选举安全规则限制，确保选举出来的 Leader 就含有最新数据， 避免了 Zab 协议的 Phase 1 阶段准 Leader 收集各个节点数据信息并复制到自身，也就是将 Phase 0 和 Phase 1 进行了合并。Recovery Phase。各个 Follower 发送自己的最新数据信息给 Leader，Leader 根据差异情况，选择发送 SNAP、DIFF 差异数据、Truncate 指令删除冲突数据等，确保 Follower 追赶上 Leader 数据进度并保持一致。Broadcast Phase。与 Zab 论文 Broadcast Phase 一致。

总体而言，从分布式系统 CAP 维度来看，ZooKeeper 与 etcd 类似的是，它也是一个 CP 系统，在出现网络分区等错误时，它优先保障的数据一致性，牺牲的是 A 可用性。


### Consul 架构及原理
了解完 ZooKeeper 架构及原理后，我们再看看 Consul，它的架构和原理是怎样的呢？下图是[Consul 架构图](https://www.consul.io/docs/architecture)（引用自 HashiCorp 官方文档）。

![img](https://static001.geekbang.org/resource/image/c4/90/c4feaebbdbe19d3f4e09899f8cd52190.png?wh=1920*1990)



从图中你可以看到，它由 Client、Server、Gossip 协议、Raft 共识算法、两个数据中心组成。每个数据中心内的 Server 基于 Raft 共识算法复制日志，Server 节点分为 Leader、Follower 等角色。Client 通过 Gossip 协议发现 Server 地址、分布式探测节点健康状态等。那什么是 Gossip 协议呢？Gossip 中文名称叫流言协议，它是一种消息传播协议。它的核心思想其实源自我们生活中的八卦、闲聊。我们在日常生活中所看到的劲爆消息其实源于两类，一类是权威机构如国家新闻媒体发布的消息，另一类则是大家通过微信等社交聊天软件相互八卦，一传十，十传百的结果。Gossip 协议的基本工作原理与我们八卦类似，在 Gossip 协议中，如下图所示，各个节点会周期性地选择一定数量节点，然后将消息同步给这些节点。收到消息后的节点同样做出类似的动作，随机的选择节点，继续扩散给其他节点。最终经过一定次数的扩散、传播，整个集群的各个节点都能感知到此消息，各个节点的数据趋于一致。Gossip 协议被广泛应用在多个知名项目中，比如 Redis Cluster 集群版，Apache Cassandra，AWS Dynamo。



![img](https://static001.geekbang.org/resource/image/84/4d/847ae4bcb531065c2797f1c91d4f464d.png?wh=1920*989)



了解完 Gossip 协议，我们再看看架构图中的多数据中心，Consul 支持数据跨数据中心自动同步吗？你需要注意的是，虽然 Consul 天然支持多数据中心，但是多数据中心内的服务数据并不会跨数据中心同步，各个数据中心的 Server 集群是独立的。不过，Consul 提供了[Prepared Query](https://www.consul.io/api-docs/query)功能，它支持根据一定的策略返回多数据中心下的最佳的服务实例地址，使你的服务具备跨数据中心容灾。比如当你的 API 网关收到用户请求查询 A 服务，API 网关服务优先从缓存中查找 A 服务对应的最佳实例。若无缓存则向 Consul 发起一个 Prepared Query 请求查询 A 服务实例，Consul 收到请求后，优先返回本数据中心下的服务实例。如果本数据中心没有或异常则根据数据中心间 RTT 由近到远查询其它数据中心数据，最终网关可将用户请求转发给最佳的数据中心下的实例地址。了解完 Consul 的 Gossip 协议、多数据中心支持，我们再看看 Consul 是如何处理读请求的呢?Consul 支持以下三种模式的读请求：

默认（default）。默认是此模式，绝大部分场景下它能保证数据的强一致性。但在老的 Leader 出现网络分区被隔离、新的 Leader 被选举出来的一个极小时间窗口内，可能会导致 stale read。这是因为 Consul 为了提高读性能，使用的是基于 Lease 机制来维持 Leader 身份，避免了与其他节点进行交互确认的开销。强一致性（consistent）。强一致性读与 etcd 默认线性读模式一样，每次请求需要集群多数节点确认 Leader 身份，因此相比 default 模式读，性能会有所下降。弱一致性（stale)。任何节点都可以读，无论它是否 Leader。可能读取到陈旧的数据，类似 etcd 的串行读。这种读模式不要求集群有 Leader，因此当集群不

### 重点特性比较
初步了解完 etcd、ZooKeeper、Consul 架构及原理后，你可以看到，他们都是基于共识算法实现的强一致的分布式存储系统，并都提供了多种模式的读机制。除了以上共性，那么它们之间有哪些差异呢？ 下表是 etcd 开源社区总结的一个[详细对比项](https://etcd.io/docs/current/learning/why/)，我们就从并发原语、健康检查及服务发现、数据模型、Watch 特性等功能上详细比较下它们功能和区别。

![img](https://static001.geekbang.org/resource/image/4d/50/4d0d9a05790f8ee9b66daf66ea741a50.jpg?wh=622*1147)


### 并发原语
etcd 和 ZooKeeper、Consul 的典型应用场景都是分布式锁、Leader 选举，以上场景就涉及到并发原语控制。然而 etcd 和 ZooKeeper 并未提供原生的分布式锁、Leader 选举支持，只提供了核心的基本数据读写、并发控制 API，由应用上层去封装。为了帮助开发者更加轻松的使用 etcd 去解决分布式锁、Leader 选举等问题，etcd 社区提供了[concurrency](https://github.com/etcd-io/etcd/tree/v3.4.9/clientv3/concurrency) 包来实现以上功能。同时，在 etcdserver 中内置了 Lock 和 Election 服务，不过其也是基于 concurrency 包做了一层封装而已，clientv3 并未提供 Lock 和 Election 服务 API 给 Client 使用。 ZooKeeper 所属的 Apache 社区提供了[Apache Curator Recipes](http://curator.apache.org/curator-recipes/index.html)库来帮助大家快速使用分布式锁、Leader 选举功能。相比 etcd、ZooKeeper 依赖应用层基于 API 上层封装，Consul 对分布式锁就提供了[原生的支持](https://www.consul.io/commands/lock)，可直接通过命令行使用。总体而言，etcd、ZooKeeper、Consul 都能解决分布式锁、Leader 选举的痛点，在选型时，你可能会重点考虑其提供的 API 语言是否与业务服务所使用的语言一致。


### 健康检查、服务发现
分布式协调服务的另外一个核心应用场景是服务发现、健康检查。与并发原语类似，etcd 和 ZooKeeper 并未提供原生的服务发现支持。相反，Consul 在服务发现方面做了很多解放用户双手的工作，提供了服务发现的框架，帮助你的业务快速接入，并提供了 HTTP 和 DNS 两种获取服务方式。比如下面就是通过 DNS 的方式获取服务地址：

```
$ dig @127.0.0.1 -p 8600 redis.service.dc1.consul. ANY
```
最重要的是它还集成了分布式的健康检查机制。与 etcd 和 ZooKeeper 健康检查不一样的是，它是一种基于 client、Gossip 协议、分布式的健康检查机制，具备低延时、可扩展的特点。业务可通过 Consul 的健康检查机制，实现 HTTP 接口返回码、内存乃至磁盘空间的检测。Consul 提供了[多种机制给你注册健康检查](https://learn.hashicorp.com/tutorials/consul/service-registration-health-checks)，如脚本、HTTP、TCP 等。脚本是怎么工作的呢？介绍 Consul 架构时，我们提到过的 Agent 角色的任务之一就是执行分布式的健康检查。比如你将如下脚本放在 Agent 相应目录下，当 Linux 机器内存使用率超过 70% 的时候，它会返回告警状态。

```
{
  ​"check": 
    ​"id": "mem-util"
    ​"name": "Memory utilization"
    ​"args": 
      ​"/bin/sh"
      ​"-c"
      ​"/usr/bin/free | awk '/Mem/{printf($3/$2*100)}' | awk '{ print($0); if($1 > 70) exit 1;}'
    ​]
    ​"interval": "10s"
    ​"timeout": "1s
  }​
}
```

相比 Consul，etcd、ZooKeeper 它们提供的健康检查机制和能力就非常有限了。etcd 提供了 Lease 机制来实现活性检测。它是一种中心化的健康检查，依赖用户不断地发送心跳续租、更新 TTL。ZooKeeper 使用的是一种名为临时节点的状态来实现健康检查。当 client 与 ZooKeeper 节点连接断掉时，ZooKeeper 就会删除此临时节点的 key-value 数据。它比基于心跳机制更复杂，也给 client 带去了更多的复杂性，所有 client 必须维持与 ZooKeeper server 的活跃连接并保持存活。



### 数据模型比较
从并发原语、健康检查、服务发现等维度了解完 etcd、ZooKeeper、Consul 的实现区别之后，我们再从数据模型上对比下三者。首先 etcd 正如我们在07节 MVCC 和10节 boltdb 所介绍的，它是个扁平的 key-value 模型，内存索引通过 B-tree 实现，数据持久化存储基于 B+ tree 的 boltdb，支持范围查询、适合读多写少，可容纳数 G 的数据。[ZooKeeper 的数据模型](https://www.usenix.org/legacy/event/atc10/tech/full_papers/Hunt.pdf)如下。

![img](https://static001.geekbang.org/resource/image/93/fb/93edd0575e5a5a1080dac40415b779fb.png?wh=1012*588)

如上图所示，它是一种层次模型，你可能已经发现，etcd v2 的内存数据模型与它是一样的。ZooKeeper 作为分布式协调服务的祖师爷，早期 etcd v2 的确就是参考它而设计的。ZooKeeper 的层次模型中的每个节点叫 Znode，它分为持久性和临时型两种。

持久性顾名思义，除非你通过 API 删除它，否则它将永远存在。临时型是指它与客户端会话绑定，若客户端会话结束或出现异常中断等，它都将被 ZooKeeper server 自动删除，被广泛应用于活性检测。

同时你创建节点的时候，还可以指定一个顺序标识，这样节点名创建出来后就具有顺序性，一般应用于分布式选举等场景中。那 ZooKeeper 是如何实现以上层次模型的呢？ZooKeeper 使用的是内存 ConcurrentHashMap 来实现此数据结构，因此具有良好的读性能。但是受限于内存的瓶颈，一般 ZooKeeper 的数据库文件大小是几百 M 左右。Consul 的数据模型及存储是怎样的呢？

它也提供了常用 key-value 操作，它的存储引擎是基于[Radix Tree](https://en.wikipedia.org/wiki/Radix_tree#)实现的[go-memdb](https://github.com/hashicorp/go-memdb)，要求 value 大小不能超过 512 个字节，数据库文件大小一般也是几百 M 左右。与 boltdb 类似，它也支持事务、MVCC。


### Watch 特性比较
接下来我们再看看 Watch 特性的比较。正在我在 08 节 Watch 特性中所介绍的，etcd v3 的 Watch 是基于 MVCC 机制实现的，而 Consul 是采用滑动窗口实现的。Consul 存储引擎是基于Radix Tree实现的，因此它不支持范围查询和监听，只支持前缀查询和监听，而 etcd 都支持。相比 etcd、Consul，ZooKeeper 的 Watch 特性有更多的局限性，它是个一次性触发器。在 ZooKeeper 中，client 对 Znode 设置了 Watch 时，如果 Znode 内容发生改变，那么 client 就会获得 Watch 事件。然而此 Znode 再次发生变化，那 client 是无法收到 Watch 事件的，除非 client 设置了新的 Watch。


### 其他比较
最后我们再从其他方面做些比较。线性读。etcd 和 Consul 都支持线性读，而 ZooKeeper 并不具备。权限机制比较。etcd 实现了 RBAC 的权限校验，而 ZooKeeper 和 Consul 实现的 ACL。事务比较。etcd 和 Consul 都提供了简易的事务能力，支持对字段进行比较，而 ZooKeeper 只提供了版本号检查能力，功能较弱。多数据中心。在多数据中心支持上，只有 Consul 是天然支持的，虽然它本身不支持数据自动跨数据中心同步，但是它提供的服务发现机制、[Prepared Query](https://www.consul.io/api-docs/query)功能，赋予了业务在一个可用区后端实例故障时，可将请求转发到最近的数据中心实例。而 etcd 和 ZooKeeper 并不支持。

### 小结
最后我们来小结下今天的内容。首先我和你从顶层视角介绍了 etcd、ZooKeeper、Consul 基本架构及核心原理。从共识算法角度上看，etcd、Consul 是基于 Raft 算法实现的数据复制，ZooKeeper 则是基于 Zab 算法实现的。Raft 算法由 Leader 选举、日志同步、安全性组成，而 Zab 协议则由 Leader 选举、发现、同步、广播组成。无论 Leader 选举还是日志复制，它们都需要集群多数节点存活、确认才能继续工作。从 CAP 角度上看，在发生网络分区时，etcd、Consul、ZooKeeper 都是一个 CP 系统，无法写入新数据。同时，etcd、Consul、ZooKeeper 提供了各种模式的读机制，总体上可分为强一致性读、非强一致性读。其中 etcd 和 Consul 则提供了线性读，ZooKeeper 默认是非强一致性读，不过业务可以通过 sync() 接口，等待 Follower 数据追赶上 Leader 进度，以读取最新值。接下来我从并发原语、健康检查、服务发现、数据模型、Watch 特性、多数据中心比较等方面和你重点介绍了三者的实现与区别。其中 Consul 提供了原生的分布式锁、健康检查、服务发现机制支持，让业务可以更省心，不过 etcd 和 ZooKeeper 也都有相应的库，帮助你降低工作量。Consul 最大的亮点则是对多数据中心的支持。最后如果业务使用 Go 语言编写的，国内一般使用 etcd 较多，文档、书籍、最佳实践案例丰富。Consul 在国外应用比较多，中文文档及实践案例相比 etcd 较少。ZooKeeper 一般是 Java 业务使用较多，广泛应用在大数据领域。另外 Nacos 也是个非常优秀的开源项目，支持服务发现、配置管理等，是 Java 业务的热门选择。

## 24 | 运维：如何构建高可靠的etcd集群运维体系？

在使用 etcd 过程中，我们经常会面临着一系列问题与选择，比如：etcd 是使用虚拟机还是容器部署，各有什么优缺点？如何及时发现 etcd 集群隐患项（比如数据不一致）？如何及时监控及告警 etcd 的潜在隐患（比如 db 大小即将达到配额）？如何优雅的定时、甚至跨城备份 etcd 数据？如何模拟磁盘 IO 等异常来复现 Bug、故障？

今天，我就和你聊聊如何解决以上问题。我将通过从 etcd 集群部署、集群组建、监控体系、巡检、备份及还原、高可用、混沌工程等维度，带你了解如何构建一个高可靠的 etcd 集群运维体系。希望通过这节课，让你对 etcd 集群运维过程中可能会遇到的一系列问题和解决方案有一定的了解，帮助你构建高可靠的 etcd 集群运维体系，助力业务更快、更稳地运行。

### 整体解决方案
那要如何构建高可靠的 etcd 集群运维体系呢?我通过下面这个思维脑图给你总结了 etcd 运维体系建设核心要点，它由 etcd 集群部署、成员管理、监控及告警体系、备份及还原、巡检、高可用及自愈、混沌工程等维度组成。

![img](https://static001.geekbang.org/resource/image/80/c2/803b20362b21d13396ee099f413968c2.png?wh=1920*1409)


### 集群部署
要想使用 etcd 集群，我们面对的第一个问题是如何选择合适的方案去部署 etcd 集群。首先是计算资源的选择，它本质上就是计算资源的交付演进史，分别如下：

物理机；虚拟机；裸容器（如 Docker 实例）；Kubernetes 容器编排。

物理机资源交付慢、成本高、扩缩容流程费时，一般情况下大部分业务团队不再考虑物理机，除非是超大规模的上万个节点的 Kubernetes 集群，对 CPU、内存、网络资源有着极高诉求。虚拟机是目前各个云厂商售卖的主流实例，无论是基于 KVM 还是 Xen 实现，都具有良好的稳定性、隔离性，支持故障热迁移，可弹性伸缩，被 etcd、数据库等存储业务大量使用。在基于物理机和虚拟机的部署方案中，我推荐你使用 ansible、puppet 等自动运维工具，构建标准、自动化的 etcd 集群搭建、扩缩容流程。基于 ansible 部署 etcd 集群可以拆分成以下若干个任务:

下载及安装 etcd 二进制到指定目录；将 etcd 加入 systemd 等服务管理；为 etcd 增加配置文件，合理设置相关参数；为 etcd 集群各个节点生成相关证书，构建一个安全的集群；组建集群版（静态配置、动态配置，发现集群其他节点）；开启 etcd 服务，启动 etcd 集群。

详细你可以参考 digitalocean这篇[博客文章](https://www.digitalocean.com/community/tutorials/how-to-set-up-and-secure-an-etcd-cluster-with-ansible-on-ubuntu-18-04)，它介绍了如何使用 ansible 去部署一个安全的 etcd 集群，并给出了对应的 yaml 任务文件。容器化部署则具有极速的交付效率、更灵活的资源控制、更低的虚拟化开销等一系列优点。自从 Docker 诞生后，容器化部署就风靡全球。有的业务直接使用裸 Docker 容器来跑 etcd 集群。然而裸 Docker 容器不具备调度、故障自愈、弹性扩容等特性，存在较大局限性。随后为了解决以上问题，诞生了以 Kubernetes、Swarm 为首的容器编排平台，Kubernetes 成为了容器编排之战中的王者，大量业务使用 Kubernetes 来部署 etcd、ZooKeeper 等有状态服务。在开源社区中，也诞生了若干个 etcd 的 Kubernetes 容器化解决方案，分别如下：

etcd-operator；bitnami etcd/statefulset；etcd-cluster-operator；openshit/cluster-etcd-operator；kubeadm。

[etcd-operator](https://github.com/coreos/etcd-operator)目前已处于 Archived 状态，无人维护，基本废弃。同时它是基于裸 Pod 实现的，要做好各种备份。在部分异常情况下存在集群宕机、数据丢失风险，我仅建议你使用它的数据备份 etcd-backup-operator。

[bitnami etcd](https://bitnami.com/stack/etcd/helm)提供了一个 helm 包一键部署 etcd 集群，支持各个云厂商，支持使用 PV、PVC 持久化存储数据，底层基于 StatefulSet 实现，较稳定。目前不少开源项目使用的是它。你可以通过如下 helm 命令，快速在 Kubernete 集群中部署一个 etcd 集群。



```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-release bitnami/etcd
```
etcd-cluster-operator和 openshit/cluster-etcd-operator比较小众，目前 star 不多，但是有相应的开发者维护，你可参考下它们的实现思路，与 etcd-operator 基于 Pod、bitnami etcd 基于 Statefulset 实现不一样的是，它们是基于 ReplicaSet 和 Static Pod 实现的。最后要和你介绍的是kubeadm，它是 Kubernetes 集群中的 etcd 高可用部署方案的提供者，kubeadm 是基于 Static Pod 部署 etcd 集群的。Static Pod 相比普通 Pod 有其特殊性，它是直接由节点上的 kubelet 进程来管理，无需通过 kube-apiserver。创建 Static Pod 方式有两种，分别是配置文件和 HTTP。kubeadm 使用的是配置文件，也就是在 kubelet 监听的静态 Pod 目录下（一般是 /etc/kubernetes/manifests）放置相应的 etcd Pod YAML 文件即可，如下图所示。

![img](https://static001.geekbang.org/resource/image/d7/05/d7c28814d3f83ff4ef474df72b10b305.png?wh=1920*1007)



注意在这种部署方式中，部署 etcd 的节点需要部署 docker、kubelet、kubeadm 组件，依赖较重。


### 集群组建
和你聊完 etcd 集群部署的几种模式和基本原理后，我们接下来看看在实际部署过程中最棘手的部分，那就是集群组建。因为集群组建涉及到 etcd 成员管理的原理和节点发现机制。在特别放送里，超凡已通过一个诡异的故障案例给你介绍了成员管理的原理，并深入分析了 etcd 集群添加节点、新建集群、从备份恢复等场景的核心工作流程。etcd 目前通过一次只允许添加一个节点的方式，可安全的实现在线成员变更。你要特别注意，当变更集群成员节点时，节点的 initial-cluster-state 参数的取值可以是 new 或 existing。

new，一般用于初始化启动一个新集群的场景。当设置成 new 时，它会根据 initial-cluster-token、initial-cluster 等参数信息计算集群 ID、成员 ID 信息。existing，表示 etcd 节点加入一个已存在的集群，它会根据 peerURLs 信息从 Peer 节点获取已存在的集群 ID 信息，更新自己本地配置、并将本身节点信息发布到集群中。

那么当你要组建一个三节点的 etcd 集群的时候，有哪些方法呢?在 etcd 中，无论是 Leader 选举还是日志同步，都涉及到与其他节点通信。因此组建集群的第一步得知道集群总成员数、各个成员节点的 IP 地址等信息。这个过程就是发现（Discovery）。目前 etcd 主要通过两种方式来获取以上信息，分别是 static configuration 和 dynamic service discovery。static configuration 是指集群总成员节点数、成员节点的 IP 地址都是已知、固定的，根据我们上面介绍的 initial-cluster-state 原理，有如下两个方法可基于静态配置组建一个集群。

方法 1，三个节点的 initial-cluster-state 都配置为 new，静态启动，initial-cluster 参数包含三个节点信息即可，详情你可参考[社区文档](https://etcd.io/docs/v3.4.0/op-guide/clustering/)。方法 2，第一个节点 initial-cluster-state 设置为 new，独立成集群，随后第二和第三个节点都为 existing，通过扩容的方式，不断加入到第一个节点所组成的集群中。

如果成员节点信息是未知的，你可以通过 dynamic service discovery 机制解决。etcd 社区还提供了通过公共服务来发现成员节点信息，组建集群的方案。它的核心是集群内的各个成员节点向公共服务注册成员地址等信息，各个节点通过公共服务来发现彼此，你可以参考[官方详细文档](https://etcd.io/docs/v3.4/dev-internal/discovery_protocol/)。


### 监控及告警体系
当我们把集群部署起来后，在业务开始使用之前，部署监控是必不可少的一个环节，它是我们保障业务稳定性，提前发现风险、隐患点的重要核心手段。那么要如何快速监控你的 etcd 集群呢？正如我在14和15里和你介绍延时、内存时所提及的，etcd 提供了丰富的 metrics 来展示整个集群的核心指标、健康度。metrics 按模块可划分为磁盘、网络、MVCC 事务、gRPC RPC、etcdserver。磁盘相关的 metrics 及含义如下图所示。

![img](https://static001.geekbang.org/resource/image/7b/a5/7b3df60d26f5363e36100525a44472a5.png?wh=1920*616)

网络相关的 metrics 及含义如下图所示。

![img](https://static001.geekbang.org/resource/image/da/32/da489a9796a016dc2yy99e101d9ab832.png?wh=1920*529)

mvcc 相关的较多，我在下图中列举了部分其含义，如下所示。



![img](https://static001.geekbang.org/resource/image/d1/51/d17446f657b110afd874yyea87176051.png?wh=1920*564)



etcdserver 相关的如下，集群是否有 leader、堆积的 proposal 数等都在此模块。

![img](https://static001.geekbang.org/resource/image/cb/6e/cbb95c525a6748bfaee48e95ca622f6e.png?wh=1920*759)

更多 metrics，你可以通过如下方法查看。

```
curl 127.0.0.1:2379/metrics
```

了解常见的 metrics 后，我们只需要配置 Prometheus 服务，采集 etcd 集群的 2379 端口的 metrics 路径。采集的方案一般有两种，[静态配置](https://etcd.io/docs/v3.4/op-guide/monitoring/)和动态配置。静态配置是指添加待监控的 etcd target 到 Prometheus 配置文件，如下所示。


```
global:
  scrape_interval: 10s
scrape_configs:
  - job_name: test-etcd
    static_configs:
    - targets:
 ['10.240.0.32:2379','10.240.0.33:2379','10.240.0.34:2379']
```

静态配置的缺点是每次新增集群、成员变更都需要人工修改配置，而动态配置就可解决这个痛点。动态配置是通过 Prometheus-Operator 的提供 ServiceMonitor 机制实现的，当你想采集一个 etcd 实例时，若 etcd 服务部署在同一个 Kubernetes 集群，你只需要通过 Kubernetes 的 API 创建一个如下的 ServiceMonitor 资源即可。若 etcd 集群与 Promehteus-Operator 不在同一个集群，你需要去创建、更新对应的集群 Endpoint。那 Prometheus 是如何知道该采集哪些服务的 metrics 信息呢?答案 ServiceMonitor 资源通过 Namespace、Labels 描述了待采集实例对应的 Service Endpoint。
```
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: prometheus-prometheus-oper-kube-etcd
  namespace: monitoring
spec:
  endpoints:
  - bearerTokenFile: /var/run/secrets/kubernetes.io/serviceaccount/token
    port: http-metrics
    scheme: https
    tlsConfig:
      caFile: /etc/prometheus/secrets/etcd-certs/ca.crt
      certFile: /etc/prometheus/secrets/etcd-certs/client.crt
      insecureSkipVerify: true
      keyFile: /etc/prometheus/secrets/etcd-certs/client.key
  jobLabel: jobLabel
  namespaceSelector:
    matchNames:
    - kube-system
  selector:
    matchLabels:
      app: prometheus-operator-kube-etcd
      release: prometheus
```
采集了 metrics 监控数据后，下一步就是要基于 metrics 监控数据告警了。你可以通过 Prometheus 和Alertmanager组件实现，那你应该为哪些核心指标告警呢？当然是影响集群可用性的最核心的 metric。比如是否有 Leader、Leader 切换次数、WAL 和事务操作延时。etcd 社区提供了一个[丰富的告警规则](https://github.com/etcd-io/etcd/blob/v3.4.9/Documentation/op-guide/etcd3_alert.rules)，你可以参考下。最后，为了方便你查看 etcd 集群运行状况和提升定位问题的效率，你可以基于采集的 metrics 配置个[grafana 可视化面板](https://github.com/etcd-io/etcd/blob/v3.4.9/Documentation/op-guide/grafana.json)。下面我给你列出了集群是否有 Leader、总的 key 数、总的 watcher 数、出流量、WAL 持久化延时的可视化面板。

![img](https://static001.geekbang.org/resource/image/a3/9f/a3b42d1e81dd706897edf32ecbc65f9f.png?wh=848*482)



![img](https://static001.geekbang.org/resource/image/d3/7d/d3bc1f984ea8b2e301471ef2923d1b7d.png?wh=1306*366)

![img](https://static001.geekbang.org/resource/image/yy/9f/yy73b00dd4d48d473c1d900c96dd0a9f.png?wh=1308*358)



![img](https://static001.geekbang.org/resource/image/2d/25/2d28317yyc38957ae2125e460b83f825.png?wh=1282*362)



![img](https://static001.geekbang.org/resource/image/9c/b9/9c471d05b1452c4f0aa8yy24c79915b9.png?wh=1268*418)


### 备份及还原
监控及告警就绪后，就可以提供给业务在生产环境使用了吗？当然不行，数据是业务的安全红线，所以你还需要做好最核心的数据备份工作。如何做呢？主要有以下方法，首先是通过 etcdctl snapshot 命令行人工备份。在发起重要变更的时候，你可以通过如下命令进行备份，并查看快照状态。

```
ETCDCTL_API=3 etcdctl --endpoints $ENDPOINT 
snapshot save snapshotdb
ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshotdb
```
其次是通过定时任务进行定时备份，建议至少每隔 1 个小时备份一次。然后是通过[etcd-backup-operator](https://github.com/coreos/etcd-operator/blob/master/doc/user/walkthrough/backup-operator.md#:~:text=etcd%20backup%20operator%20backs%20up,storage%20such%20as%20AWS%20S3.)进行自动化的备份，类似 ServiceMonitor，你可以通过创建一个备份任务 CRD 实现。CRD 如下：

```
apiVersion: "etcd.database.coreos.com/v1beta2"
kind: "EtcdBackup"
metadata:
  name: example-etcd-cluster-periodic-backup
spec:
  etcdEndpoints: [<etcd-cluster-endpoints>]
  storageType: S3
  backupPolicy:
    # 0 > enable periodic backup
    backupIntervalInSecond: 125
    maxBackups: 4
  s3:
    # The format of "path" must be: "<s3-bucket-name>/<path-to-backup-file>"
    # e.g: "mybucket/etcd.backup"
    path: <full-s3-path>
    awsSecret: <aws-secret>
```
最后你可以通过给 etcd 集群增加 Learner 节点，实现跨地域热备。因 Learner 节点属于非投票成员的节点，因此它并不会影响你集群的性能。它的基本工作原理是当 Leader 收到写请求时，它会通过 Raft 模块将日志同步给 Learner 节点。你需要注意的是，在 etcd 3.4 中目前只支持 1 个 Learner 节点，并且只允许串行读。

### 巡检
完成集群部署、了解成员管理、构建好监控及告警体系并添加好定时备份策略后，这时终于可以放心给业务使用了。然而在后续业务使用过程中，你可能会遇到各类问题，而这些问题很可能是 metrics 监控无法发现的，比如如下：

etcd 集群因重启进程、节点等出现数据不一致；业务写入大 key-value 导致 etcd 性能骤降；业务异常写入大量 key 数，稳定性存在隐患；业务少数 key 出现写入 QPS 异常，导致 etcd 集群出现限速等错误；重启、升级 etcd 后，需要人工从多维度检查集群健康度；变更 etcd 集群过程中，操作失误可能会导致 etcd 集群出现分裂；......

因此为了实现高效治理 etcd 集群，我们可将这些潜在隐患总结成一个个自动化检查项，比如：如何高效监控 etcd 数据不一致性？如何及时发现大 key-value?如何及时通过监控发现 key 数异常增长？如何及时监控异常写入 QPS?如何从多维度的对集群进行自动化的健康检测，更安心变更？......


如何将这些 etcd 的最佳实践策略反哺到现网大规模 etcd 集群的治理中去呢？答案就是巡检。

参考 ServiceMonitor 和 EtcdBackup 机制，你同样可以通过 CRD 的方式描述此巡检任务，然后通过相应的 Operator 实现此巡检任务。比如下面就是一个数据一致性巡检的 YAML 文件，其对应的 Operator 组件会定时、并发检查其关联的 etcd 集群各个节点的 key 差异数。

```
apiVersion: etcd.cloud.tencent.com/v1beta1
kind: EtcdMonitor
metadata:  
creationTimestamp: "2020-06-15T12:19:30Z"  
generation: 1  
labels:    
clusterName: gz-qcloud-etcd-03    
region: gz    
source: etcd-life-cycle-operator  
name: gz-qcloud-etcd-03-etcd-node-key-diff  
namespace: gz
spec:  
clusterId: gz-qcloud-etcd-03  
metricName: etcd-node-key-diff  
metricProviderName: cruiser  
name: gz-qcloud-etcd-03  
productName: tke  
region: gz
status:  
records:  
- endTime: "2021-02-25T11:22:26Z"    
message: collectEtcdNodeKeyDiff,etcd cluster gz-qcloud-etcd-03,total key num is      
122143,nodeKeyDiff is 0     
startTime: "2021-02-25T12:39:28Z"  
updatedAt: "2021-02-25T12:39:28Z"
```

### 高可用及自愈
通过以上机制，我们已经基本建设好一个高可用的 etcd 集群运维体系了。最后再给你提供几个集群高可用及自愈的小建议：若 etcd 集群性能已满足业务诉求，可容忍一定的延时上升，建议你将 etcd 集群做高可用部署，比如对 3 个节点来说，把每个节点部署在独立的可用区，可容忍任意一个可用区故障。逐步尝试使用 Kubernetes 容器化部署 etcd 集群。当节点出现故障时，能通过 Kubernetes 的自愈机制，实现故障自愈。设置合理的 db quota 值，配置合理的压缩策略，避免集群 db quota 满从而导致集群不可用的情况发生。

### 混沌工程
在使用 etcd 的过程中，你可能会遇到磁盘、网络、进程异常重启等异常导致的故障。如何快速复现相关故障进行问题定位呢？答案就是混沌工程。一般常见的异常我们可以分为如下几类：磁盘 IO 相关的。比如模拟磁盘 IO 延时上升、IO 操作报错。之前遇到的一个底层磁盘硬件异常导致 IO 延时飙升，最终触发了 etcd 死锁的 Bug，我们就是通过模拟磁盘 IO 延时上升后来验证的。网络相关的。比如模拟网络分区、网络丢包、网络延时、包重复等。进程相关的。比如模拟进程异常被杀、重启等。之前遇到的一个非常难定位和复现的数据不一致 Bug，我们就是通过注入进程异常重启等故障，最后成功复现。压力测试相关的。比如模拟 CPU 高负载、内存使用率等。

开源社区在混沌工程领域诞生了若干个优秀的混沌工程项目，如 chaos-mesh、chaos-blade、litmus。这里我重点和你介绍下chaos-mesh，它是基于 Kubernetes 实现的云原生混沌工程平台，下图是其架构图（引用自社区）。

![img](https://static001.geekbang.org/resource/image/b8/a7/b87d187ea2ab60d824223662fd6033a7.png?wh=1920*1287)



为了实现以上异常场景的故障注入，chaos-mesh 定义了若干种资源类型，分别如下：IOChaos，用于模拟文件系统相关的 IO 延时和读写错误等。NetworkChaos，用于模拟网络延时、丢包等。PodChaos，用于模拟业务 Pod 异常，比如 Pod 被杀、Pod 内的容器重启等。StressChaos，用于模拟 CPU 和内存压力测试。

当你希望给 etcd Pod 注入一个磁盘 IO 延时的故障时，你只需要创建此 YAML 文件就好。


```
apiVersion: chaos-mesh.org/v1alpha1
kind: IoChaos
metadata:
  name: io-delay-example
spec:
  action: latency
  mode: one
  selector:
    labelSelectors:
      app: etcd
  volumePath: /var/run/etcd
  path: '/var/run/etcd/**/*'
  delay: '100ms'
  percent: 50
  duration: '400s'
  scheduler:
    cron: '@every 10m'
```


### 小结
最后我们来小结下今天的内容。今天我通过从集群部署、集群组建、监控及告警体系、备份、巡检、高可用、混沌工程几个维度，和你深入介绍了如何构建一个高可靠的 etcd 集群运维体系。在集群部署上，当你的业务集群规模非常大、对稳定性有着极高的要求时，推荐使用大规格、高性能的物理机、虚拟机独占部署，并且使用 ansible 等自动化运维工具，进行标准化的操作 etcd，避免人工一个个修改操作。对容器化部署来说，Kubernetes 场景推荐你使用 kubeadm，其他场景可考虑分批、逐步使用 bitnami 提供的 etcd helm 包，它是基于 statefulset、PV、PVC 实现的，各大云厂商都广泛支持，建议在生产环境前，多验证各个极端情况下的行为是否符合你的预期。在集群组建上，各个节点需要一定机制去发现集群中的其他成员节点，主要可分为 static configuration 和 dynamic service discovery。static configuration 是指集群中各个成员节点信息是已知的，dynamic service discovery 是指你可以通过服务发现组件去注册自身节点信息、发现集群中其他成员节点信息。另外我和你介绍了重要参数 initial-cluster-state 的含义，它也是影响集群组建的一个核心因素。在监控及告警体系上，我和你介绍了 etcd 网络、磁盘、etcdserver、gRPC 核心的 metrics。通过修改 Prometheues 配置文件，添加 etcd target，你就可以方便的采集 etcd 的监控数据。我还给你介绍了 ServiceMonitor 机制，你可通过它实现动态新增、删除、修改待监控的 etcd 实例，灵活的、高效的采集 etcd Metrcis。备份及还原上，重点和你介绍了 etcd snapshot 命令，etcd-backup-operator 的备份任务 CRD 机制，推荐使用后者。最后是巡检、混沌工程，它能帮助我们高效治理 etcd 集群，及时发现潜在隐患，低成本、快速的复现 Bug 和故障等。

## 特别放送 | 成员变更：为什么集群看起来正常，移除节点却会失败呢？

### 从一次诡异的故障说起
首先让我们来看一个实际生产环境中遇到的案例。某天我收到了一个小伙伴的紧急求助，有一个 3 节点集群，其中一个节点发生了故障后，由于不规范变更，没有先将节点剔除集群，而是直接删除了数据目录，然后重启了节点。之后该节点就不停的 panic，此时其他两个节点正常。诡异的是，此时执行 member remove 操作却报错集群没有 Leader，但是用 endpoint status 命令可以看到集群是有 Leader 存在的。更加奇怪的是，过了几个小时后，该节点又自动恢复了（如下图）。

![img](https://static001.geekbang.org/resource/image/47/3b/47e73f39f751f9a9430e8da4d2896f3b.png?wh=1920*314)

![img](https://static001.geekbang.org/resource/image/82/bc/82689859250cayye421a9e4a821799bc.png?wh=1616*172)

![img](https://static001.geekbang.org/resource/image/a9/ec/a972a5849409a8c8a2b8fb11880a5fec.png?wh=1920*101)

你可以先自己思考下，可能是什么原因导致了这几个问题？有没有办法能够在这种场景下快速恢复集群呢？如果暂时没什么思路，不要着急，相信学完这节课的成员变更原理后，你就能够独立分析类似的问题，并快速地提供正确、安全的恢复方式。


### 静态配置变更 VS 动态配置变更
接下来我们就来看下，要实现成员变更，都有哪些方案。最简单的方案就是将集群停服，更新所有节点配置，再重新启动集群。但很明显，这个方案会造成变更期间集群不可用。对于一个分布式高可用的服务来说，这是不可接受的。而且手工变更配置很容易因为人为原因造成配置修改错误，从而造成集群启动失败等问题发生。既然将所有节点同时关闭来更新配置我们无法接受，那么我们能否实现一个方案，通过滚动更新的方式增删节点来逐个更新节点配置，尽量减少配置更新对集群的影响呢？zookeeper 3.5.0 之前就是采用的这个方案来降低配置更新对集群可用性的影响。但这种方案还是有一定的缺点。一是要对存量节点配置进行手动更新，没有一个很好的校验机制，如果配置更新错误的话很容易对集群造成影响。二是滚动更新配置的过程中节点要进行重启，存量的连接要断开重连。在连接数和负载较高的场景下，大量连接重连也会对集群稳定性造成一定的影响。针对这两个问题，有没有进一步的优化空间呢？作为程序员，我们的目标肯定是要尽量消除人工操作，将手工操作自动化，这样才能避免人为错误。那么我们能否能够在配置实际应用之前，通过程序来做好一系列的检查工作，当所有检查通过后，再实际应用新的配置呢？同样，为了避免重启节点，我们能否通过 API 和共识算法，将新的配置动态同步到老的节点呢？etcd 目前采用的正是上面这种实现方式。它将成员变更操作分为了两个阶段（如下图）：第一个阶段，通过 API 提交成员变更信息，在 API 层进行一系列校验，尽量避免因为人为原因造成的配置错误。如果新的配置通过校验，则通过 Raft 共识算法将新的配置信息同步到整个集群，等到整个集群达成共识后，再应用新的配置。第二个阶段，启动新的节点，并实际加入到集群中（或者移除老的节点，然后老节点自动退出）。

![img](https://static001.geekbang.org/resource/image/ce/06/ce969a18ea09b228d3e8fa50f2f12b06.png?wh=1200*804)

接下来我们就先来看下。etcd 如何基于 Raft 来实现成员信息同步。

### 如何通过 Raft 实现成员信息同步
#### 成员变更流程
在04 节课中，我们已经了解到，Raft 将一致性问题拆分成了 3 个子问题，即 Leader 选举、日志复制以及安全性。基于日志复制，我们可以将成员变更信息作为一个日志条目，通过日志同步的方式同步到整个集群。那么问题来了，日志同步后，我们应该什么时候应用新的配置呢，直接应用新的配置会造成什么问题吗？

![img](https://static001.geekbang.org/resource/image/86/25/867401d323bbac288a0304d43e75f325.png?wh=822*736)



如上图所示（参考自[Raft 论文](https://web.stanford.edu/~ouster/cgi-bin/papers/raft-atc14)），当我们将 3 个节点集群扩展到 5 个节点的时候，我们可以看到，对于老的 3 节点配置来说，它的多数派是 2 个节点。而对于新的 5 节点配置集群来说，它的多数派是 3 个节点。在箭头指向的时刻，新老配置同时生效，老的配置中 Server1 和 Server2 组成了多数派，新的配置中 Server3、Server4、Server5 组成了新的多数派。此时集群中存在两个多数派，可能会选出两个 Leader，违背了安全性。那么有没有方式能避免这个问题，保证变更的安全性呢？一方面我们可以引入两阶段提交来解决这个问题，另一方面我们可以通过增加一定约束条件来达到目标。如下图所示，当我们一次只变更一个节点的时候我们可以发现，无论是从奇数节点扩缩到偶数节点，还是从偶数节点扩缩到奇数节点，扩缩容前后配置中的多数派必然有一个节点存在交叉（既存在于老的配置的多数派中，也存在于新的配置的多数派中）。我们知道在 Raft 里，竞选出的 Leader 必须获得一半以上节点投票，这就保证了选出的 Leader 必然会拥有重叠节点的投票。而一个节点在一轮投票中只能投票给一个候选者，这就保证了新老配置最终选出的 Leader 必然是一致的。

![img](https://static001.geekbang.org/resource/image/04/6f/047fa5420be1c48b5eacaabb5ff8956f.png?wh=1206*920)

因此，我们通过增加一次只变更一个成员这个约束，就可以得到一个很简单的成员变更实现方式：在一次只变更一个节点的场景下，每个节点只需要应用当前收到的日志条目中最新的成员配置即可（即便该配置当前还没有 commit）。在一个变更未结束时，禁止提交新的成员变更。

这样就保证了一个成员变更可以安全地进行，同时在变更的过程中，不影响正常的读写请求，也不会造成老的节点重启，提升了服务的稳定性。需要注意的是，etcd 并没有严格按照 Raft 论文来实现成员变更，它应用新的配置时间点是在应用层 apply 时，通知 Raft 模块进行 ApplyConfChange 操作来进行配置切换，而不是在将配置变更追加到 Raftlog 时立刻进行切换。到目前为止，etcd 就完整地实现了一个成员信息同步的流程。如果是扩容的话，接下来只需要启动之前配置的新节点就可以了。


### 为什么需要 Learner
那么这个实现方案有没有什么风险呢？我们一起来分析下。举个例子，当我们将集群从 3 节点扩容到 4 节点的时候，集群的法定票数（quorum）就从 2 变成了 3。而我们新加的节点在刚启动的时候，是没有任何日志的，这时就需要从 Leader 同步快照才能对外服务。如果数据量比较大的话，快照同步耗时会比较久。在这个过程中如果其他节点发生了故障，那么集群可用节点就变成了 2 个。而在 4 节点集群中，日志需要同步到 3 个以上节点才能够写入成功，此时集群是无法写入的。由于法定票数增加，同时新节点同步日志时间长不稳定，从而增大了故障的概率。那么我们是否能通过某种方式来尽量缩短日志同步的时间呢？答案就是 Learner 节点（在 Raft 论文中也叫 catch up）。etcd 3.4 实现了 Leaner 节点的能力，新节点可以以 Learner 的形式加入到集群中。Learner 节点不参与投票，即加入后不影响集群现有的法定票数，不会因为它的加入而影响到集群原有的可用性。Learner 节点不能执行写操作和一致性读，Leader 会将日志同步给 Learner 节点，当 Learner 节点的日志快追上 Leader 节点时（etcd  3.4  Learner 已同步的日志条目 Index 达到 Leader 的 90% 即认为 ready），它就成为 Ready 状态，可被提升为 Voting Member。此时将 Learner 提升为 Voting Member，可以大大缩短日志同步时间，降低故障的概率。另外，由于 Learner 节点不参与投票，因此即使因为网络问题同步慢也不会影响集群读写性能和可用性，可以利用这个特性来方便的实现异地热备的能力。


### 联合一致性（joint consensus）
虽然一次添加一个节点在实现上可以降低很大的复杂度，但它同样也有一些缺陷。例如在跨 zone 容灾的场景下，假设一个集群有三个节点 A，B，C，分别属于不同的 zone，你无法在不影响跨多 zone 容灾能力的情况下替换其中一个节点。假设我们要用同一个 zone 的 D 节点来替换 C 节点（如下图）：如果我们采用先增后减的形式，先将 D 加到集群中，此时集群节点数变为了 4，法定票数变为了 3。如果 C，D 所在的 zone 挂掉，则集群只剩下两个可用节点，变为不可用状态。如果我们采用先减后增的形式，先将 C 节点移除，此时集群中剩 2 个节点，法定票数为 2。如果 A 或者 B 所在的 zone 挂掉了，集群同样不可用。

![img](https://static001.geekbang.org/resource/image/28/f6/285e3e09e32bd26667fe211ddd5601f6.png?wh=1138*1284)

当然，通过 Learner 节点可以很大程度上降低这个问题发生的概率。但我们如果能够实现多节点成员变更的话，则可以从根本上解决这个问题。多节点成员变更也是 Raft 论文中最初提到的实现成员变更的方式，为了保证成员变更的安全性，我们可以通过两阶段提交来实现同时变更多个成员，两阶段提交的实现方式有多种，在 Raft 中是通过引入一个过渡配置来实现的，即引入联合一致性（joint consensus）来解决这个问题。如下图（引用自Raft 论文）所示：

![img](https://static001.geekbang.org/resource/image/26/08/2654729e02a41e416eaf16c0bd27c508.png?wh=1374*580)



我们可以看到，Raft 引入了一个过渡配置：Cold,new。当新的配置提案发起时，Leader 会先生成 Cold,new 状态的配置。当集群处于这个配置时，需要 Cold 和 Cnew 的多数派都同意 commit，新的提案才能被 commit。当 Cold,new 被 commit 后，就可以安全切换到新的配置 Cnew 了，当 Cnew 被提交后，整个变更操作就完成了。通过引入 joint consensus，我们可以看到不会存在 Cold 和 Cnew 同时独立做决定的情况，保证了成员变更的安全性。进一步推广的话，通过引入 joint consensus，我们可以在多个成员变更过程中继续提交新的配置。但这么做不仅会带来额外的复杂度，而且基本上不会带来实际的收益。因此在工程实现上我们一般还是只允许同一时间只能进行一次成员变更，并且在变更过程中，禁止提交新的变更。etcd  3.4 的 Raft 模块实现了 joint consensus，可以允许同时对多个成员或单个成员进行变更。但目前应用层并未支持这个能力，还是只允许一次变更一个节点。它的实现仍然同 Raft 论文有一定的区别，Raft 论文是在配置变更提案追加到 Raftlog 时就切换配置，而 etcd 的 Raft 实现是在 apply 过程才进行配置切换。当 Cold,new 配置 apply 之后，就可以返回给客户端成功了。但此时变更还未完全结束，新的日志条目仍然需要 Cold 和 Cnew 多数派都同意才能够提交，Raft 模块会通过追加一个空的配置变更条目，将配置从 Cold,new 切换到 Cnew。当 Cnew apply 后，新的日志条目就只需要 Cnew 多数派同意即可，整个成员变更信息同步完成。

### 集群扩容节点完整流程
上边讲完了成员信息同步流程，我们就可以来看下向一个已有集群扩容一个新节点的整体流程是怎样的（整体流程如下图）。

![img](https://static001.geekbang.org/resource/image/91/a5/9104a0b63c45c97b26103ac47a20a3a5.png?wh=1514*1200)


首先，我们可以通过 etcdctl 或者 clientv3 库提供的 API 来向成员管理模块发起一个 MemberAdd 请求。成员管理模块在收到请求后，会根据你提供的 peer-urls 地址来构建一个 Member 成员（注意此时构建的 Member 成员的 Name 为空），然后请求 etcdserver 进行添加操作。


```
ETCDCTL_API=3 etcdctl --endpoints=http://1.1.1.1:2379 
member add node-4 --peer-urls=http://4.4.4.4:2380
```
在开启 strict-reconfig-check 的情况下（默认开启），etcdserver 会先进行一系列检查，比如检查当前集群启动的节点数是否满足法定票数要求，当前集群所有投票节点是否都存活等。检查通过后，则向 Raft 模块发起一个 ProposeConfChange 提案，带上新增的节点信息。提案在 apply 时，会通知 Raft 模块切换配置，同时更新本节点 server 维护的 member 和 peer 节点信息（如果是移除节点操作的话，被移除节点 apply 之后延时 1s etcd 进程会主动退出），并将当前的成员信息更新到 etcdserver 维护的 ConfState 结构中。在 snapshot 的时候会进行持久化（具体作用我们后边会介绍），然后返回给客户端成功。如果你用的是 etcdctl 的话，应该可以看到如下输出：

```
Member 96af95420b65e5f5 added to cluster 81a549bdbfd5c3a8

ETCD_NAME="node-4"
ETCD_INITIAL_CLUSTER="node-1=http://1.1.1.1:2380,node-2=http://2.2.2.2:2380,node-3=http://3.3.3.3:2380,node-4=https://4.4.4.4:2380"
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://4.4.4.4:2380"
ETCD_INITIAL_CLUSTER_STATE="existing"
```
通过使用命令返回的环境变量参数，我们就可以启动新的节点了（注意，这里一定要保证你的启动参数和命令返回的环境变量一致）。新节点启动时，会先校验一系列启动参数，根据是否存在 WAL 目录来判断是否是新节点，根据 initial-cluster-state 参数的值为 new 或 existing 来判断是加入新集群还是加入已存在集群。如果是已存在集群添加新节点的情况（也就是不存在 WAL 目录，且 initial-cluster-state 值为 existing。如果存在 WAL 目录，则认为是已有节点，会忽略启动参数中的 initial-cluster-state 和 initial-cluster 等参数，直接从 snapshot 中和 WAL 中获取成员列表信息），则会从配置的 peerURLs 中获取其他成员列表，连接集群来获取已存在的集群信息和成员信息，更新自己的本地配置。然后会启动 RaftNode，进行一系列的初始化操作后，etcdserver 就可以启动了。启动时，会通过 goroutine 异步执行 publish 操作，通过 Raft 模块将自己发布到集群中。在发布之前，该节点在集群内的 Name 是空，etcd 会认为 unstarted，发布时会通过 Raft 模块更新节点的 Name 和 clientURLs 到集群中，从而变成 started 状态。发布之后，该节点就可以监听客户端端口，对外提供服务了。在执行 publish 的同时，会启动监听 peer 端口，用于接收 Leader 发送的 snapshot 和日志。

### 新集群如何组建
上边介绍了已存在集群扩容的场景，那么新建集群时又是怎样的呢？新建集群和加节点的启动流程大体上一致，这里有两个不同的点：一个是在新集群创建时，构建集群的 member 信息会直接从启动参数获取，区别于加节点场景从已存在集群查询。这就要求新集群每个节点初始化配置的 initial-cluster、initial-cluster-state、initial-cluster-token 参数必须相同，因为节点依赖这个来构建集群初始化信息，参数相同才能保证编码出来的 MemberId 和 ClusterId 相同。另一个需要注意的点是在启动 Raft Node 的过程中，如果是新建集群的话，会多一步 BootStrap 流程。该流程会将 initial-cluster 中声明的 Peer 节点转换为 ConfChangeAddNode 类型的 ConfChange 日志条目追加到 Raftlog 中，并设置为 commited 状态。然后直接通过 applyConfChange 来应用该配置，并在应用层开始 apply 流程时再次 apply 该配置变更命令（这里重复应用相同配置不会有其他影响）。你知道 etcd 为什么要这么做吗？这么做的一个好处是，命令会通过 WAL 持久化，集群成员状态也会通过 snapshot 持久化。当我们遇到后续节点重启等场景时，就可以直接应用 snapshot 和 WAL 中的配置进行重放，来生成实际的成员配置，而不用再从启动参数读取。因为启动参数可能因为动态重配置而不再准确，而 snapshot 和 WAL 中的配置可以保证最新。

### 如何从备份恢复集群
除了新建集群和集群扩缩容外，备份恢复同样十分重要。在集群一半以上节点挂掉后，就只能从备份来恢复了。我们可以通过 etcdctl snapshot save 命令或者 clientv3 库提供的 snapshot API 来对集群进行备份。备份后的数据除了包含业务数据外，还包含一些集群的元数据信息（例如成员信息）。有了备份之后，我们就可以通过 etcdctl snapshot restore 命令来进行数据恢复。这个命令的参数你一定不要搞错，我建议你对照[官方文档](https://etcd.io/docs/v3.4.0/op-guide/recovery/)来。每个节点恢复数据时的 name 和 initial-advertise-peer-urls 是有区别的，如果所有节点都用一样的话，最后你可能会恢复成多个独立的集群，我曾经就见到有业务这样搞出过问题。我们接着来看下 snapshot restore 都干了哪些事情（如下图）。

![img](https://static001.geekbang.org/resource/image/f2/f3/f28bf73f76e00b62af659526f09575f3.png?wh=376*1316)



首先，它会根据你提供的参数进行一系列校验，检查 snapshot 的 hash 值等。如果检查通过的话，会创建 snap 目录并将 snapshot 拷贝到 v3 的 db 文件，设置 consistentIndex 值为当前提供的 initial-cluster 参数中包含的成员数量，并从 db 中删除老的成员信息。然后，它会根据你提供的参数信息来构建 WAL 文件和 snap 文件。从你提供的配置中来获取 peer 节点信息，并转换为 ConfChangeAddNode 类型的 ConfChange 日志条目写入 WAL 文件，同时更新 commit 值，并将 term 设置为 1。之后 snapshot restore 会将 peer 节点作为 Voters 写入 snapshot metadata 的 ConfState 中，并更新 Term 和 Index。snapshot 保存后，WAL 会随后保存当前 snapshot 的 Term 和 Index，用于索引 snapshot 文件。当每个节点的数据恢复后，我们就可以正常启动节点了。因为 restore 命令构造了 WAL 和 snapshot，因此节点启动相当于一个正常集群进行重启。在启动 Raft 模块时，会通过 snapshot 的 ConfState 来更新 Raft 模块的配置信息，并在应用层 apply 时会重放从 WAL 中获取到的 ConfChangeAddNode 类型的 ConfChange 日志条目，更新应用层和 Raft 模块配置。至此，集群恢复完成。

### 故障分析
了解完 etcd 集群成员变更的原理后，我们再回到开篇的问题，不晓得现在你有没有一个大概的思路呢？接下来就让我们运用这节课和之前学习的内容，一起来分析下这个问题。首先，这个集群初始化时是直接启动的 3 节点集群，且集群创建至今没有过成员变更。那么当删除数据重启时，异常节点会认为自己是新建集群第一次启动，所以在启动 Raft 模块时，会将 peer 节点信息转换成 ConfChangeAddNode 类型的 ConfChange 日志条目追加到 Raftlog 中，然后设置 committed Index 为投票节点数量。我们是 3 节点集群，所以此时 committed Index 设置为 3，并设置 term 为 1，然后在本地 apply 该日志条目，应用初始化配置信息，然后启动 etcdserver。Leader 在检测到该节点存活后，会向该节点发送心跳信息，同步日志条目。Leader 本地会维护每个 peer 节点的 Match 和 Next Index，Match 表示已经同步到该节点的日志条目 Index，Next 表示下一次要同步的 Index。当 Leader 向 Follower 节点发送心跳时，会从 Match 和 Leader 当前的 commit Index 中选择一个较小的，伴随心跳消息同步到 Follower 节点。Follower 节点在收到 Leader 的 commit Index 时，会更新自己本地的 commit Index。但 Follower 节点发现该 commit Index 比自己当前最新日志的 Index 还要新（按照我们之前的分析，异常节点当前最新的 Index 为 3（日志也证明了这一点），而 Leader 发送的 commit Index 是之前节点正常时的 commit 值，肯定比 3 这个值要大），便认为 raftlog 肯定有损坏或者丢失，于是异常节点就会直接 panic 退出。最后就出现了我们之前看到的不停重启不停 panic 的现象。那么为什么执行 member remove 操作会报没有 Leader 呢？我们之前提到过，执行成员变更前会进行一系列前置检查（如下图）。在移除节点时，etcd 首先会检查移除该节点后剩余的活跃节点是否满足集群法定票数要求。满足要求后，会检查该节点是否宕机（连接不通）。如果是宕机节点，则可以直接移除。

![img](https://static001.geekbang.org/resource/image/8e/39/8edb11c1c268eabda477597ce25e0f39.png?wh=828*1352)

但由于我们的节点不停重启，每次重启建立 peer 连接时会激活节点状态，因此没有统计到宕机的节点中。最后会统计集群中当前可用的节点，该统计方式要求节点必须在 5s 前激活，因为节点刚启动 5s 内认为 etcd 还没有 ready，所以不会统计到可用节点中，即当前可用节点数为 2。然后再判断移除一个可用节点后，当前剩余节点是否满足法定票数要求，我们这个案例中为 2 - 1 < 1+ ((3-1)/2)，不满足法定票数要求，所以服务端会返回 ErrUnhealthy 报错给客户端（我们这个场景其实是由于 etcd 针对不可用节点的判断没有排除异常的要移除节点导致）。由于用户当时使用的是 etcdctl v2 的 API，所以客户端最终会将该错误转换成 http code 503，客户端识别到 503，就会认为当前集群没 Leader（这里 v2 客户端代码对 v3 grpc 错误码转换判断不是很准确，有误导性），打印我们之前看到的 no Leader 错误。最后一个问题，为什么后来 panic 节点会自动恢复呢？答案是中间由于 IO 高负载，发生了心跳超时，造成了 Leader 选举。新的 Leader 选举出来后，会重置自己维护的 peer 节点的 Match Index 为 0，因此发送给异常 Follower 心跳时带上的 commit Index 即为 0。所以 Follower 不会再因为 commit Index 小于自己最新日志而 panic。而 Leader 探测到 Follower 的 Index 和自己差距太大后，就发送 snapshot 给 Follower，Follower 接收 snapshot 后恢复正常。这个 case 了解原理后，如果希望快速恢复的话也很简单：完全停掉异常 Follower 节点后，再执行 member remove，然后将节点移除，清理数据再重新加入到集群（或者通过 move-leader 命令手动触发一次 Leader 切换，但该方式比较 trick，并不通用）。以上就是这个案例的完整分析，希望通过这个 case，能让你认识到规范变更的重要性，在不了解原理的情况下，一定要按照官方文档来操作，不要凭感觉操作。


### 小结

![img](https://static001.geekbang.org/resource/image/yy/1e/yy38094b6be35a476442fd3498eb511e.png?wh=1820*1028)

最后我们来小结下今天的内容，今天我从一个诡异的成员变更故障案例讲起，为你介绍了 etcd 实现成员变更的原理，分别为你分析了 etcd 成员变更在 Raft 层和应用层的实现，并分析了各个实现方案的优缺点。其次我带你过了一遍 etcd 成员变更的演进方案：从只支持 Member 变更到支持 Learner 节点（non-voting Member），Raft 层从只支持单节点变更到支持多节点变更。成员变更的方案越来越完善、稳定，运维人员在变更期间发生故障的概率也越来越低。之后我以新增节点为例，深入为你分析了从配置提交到节点启动对外服务的完整流程，以及新集群启动和恢复过程中涉及到的成员变更原理。最后，通过我们这节课和之前的课程学到的原理，我和你一步一步深入分析了下开篇的故障问题可能发生的原因以及快速恢复的方法。希望通过这节课，让你对 etcd 成员变更方案有一个深入的了解，在遇到类似的问题时能够快速定位问题并解决，提升业务的稳定性。



