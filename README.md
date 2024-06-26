# LLM

## RAG
LLM 将从大量训练数据（语料库）中学习到知识存储在模型的参数中，但这种参数化的模型存在局限性。首先是它难以保留训练语料库中的所有知识，特别是对于那些不太常见且具体的知识。其次，由于模型参数无法动态更新，参数化知识可能会随时间过时。最后，参数的增加会导致训练和推理的计算成本增加。

因此，可以采取半参数化方法，将非参数化的语料库数据库与参数化模型相结合，这种方法就是检索增强生成（Retrival-Augmented Generation, RAG），在利用 LLM 模型回答问题或生成文本时，首先从广阔的文档库中寻找相关信息。然后，模型使用这些找到的信息来生成回答或文本，从而提高其预测的准确度。
> - 参数化知识是指：在模型训练过程中，将知识参数化，存储在模型的参数、权重中，代表模型对训练数据的理解和泛化能力，是生成回应在的基础。
> - 非参数化知识是指：存储在外部的知识源，如向量数据库中，不直接编入模型，作为一种可更新的补充信息。非参数化知识使大语言模型能够访问和利用最新或特定领域的信息，提高回应的准确性和相关性。

**RAG 优点**
- 通过关联外部知识来提高答案的准确性，有效减少了语言模型中出现的虚假信息，使得生成的回答更加准确可信。
- 使用检索技术能够识别到最新的信息，这使得 RAG 在保持回答的及时性和准确性方面，相较于只依赖训练数据的传统语言模型有明显优势。
- 输出结果透明度更高。通过引用信息来源，用户可以核实答案的准确性，这增强了人们对模型输出结果的信任。
- 具备高度的定制化能力。通过索引与特定领域相关的文本语料库，RAG 能够为不同领域提供专业的知识支持。
- 在安全性和隐私管理方面，RAG 通过数据库中设置的角色和安全控制，实现了对数据使用的更好控制。相比之下，经过微调的模型在管理数据访问权限方面可能不够明确。
- 在处理大规模数据集方面更具有扩展性。它无需更新所有参数和创建新的训练集，因此在经济效率方面更具优势。

**RAG 系统的阶段**
- 使用编码模型（如 BM25、DPR、ColBERT 等）根据问题找到相关的文档
- 生成阶段：以找到的上下文作为基础，系统生成文本。

**微调**
- 用于强化模型已有的知识、调整或定制模型的输出，以及给模型下达复杂的指令。如要精确模仿特殊的结构、艺术风格或者格式。
- 不适用于向模型中增加全新的知识，或应对需要快速迭代新场景的情况。


| 特征比较    | RAG             | 微调 (Fine-tuning) |
|--------|----------------|--------|
| 知识更新| 直接更新检索知识库，确保信息持续更新，无需频繁重新训练，非常适合动态变化的数据环境。| 存储静态数据，需要重新训练用于知识和数据的更新。存储静态数据，需要重新训练用于知识和数据的更新。| 
| 外部知识	| 擅长利用外部资源，特别适合处理文档或其他结构化/非结构化数据库。	| 可用于将预训练中外部学习到的知识与大语言模型保持一致，但对于频繁变化的数据源可能不太实用。|
| 数据处理	| 对数据的处理和操作要求极低。	| 依赖于构建高质量的数据集，有限的数据集可能无法显著提高性能。|
| 模型定制	| 侧重于信息检索和融合外部知识，但可能无法充分定制模型行为或写作风格。	| 允许根据特定风格或术语调整 LLM 行为、写作风格或特定领域知识。|
| 可解释性	| 答案能够追溯到具体的数据来源，提供更高的可解释性和可追踪性。	| 就像一个黑盒子，并不总是清楚模型为什么会做出某种反应，可解释性相对较低。|
| 计算资源	| 需要计算资源来支持检索策略和数据库相关技术。外部数据源的整合和更新需保持维护。	| 有必要准备和整理高质量的训练数据集，确定微调目标，并提供相应的计算资源。|
| 延迟要求	| 因涉及数据检索，可能带来较高的延迟。	| 经过微调的大语言模型 (LLM) 可以不通过检索直接回应，降低延迟。|
| 降低幻觉	| 由于每个回答都基于检索到的实际证据，因此本质上更不容易产生错误或虚构的回答。	| 根据特定领域的数据训练模型，有助于减少幻觉，但面对未训练过的输入时仍可能出现幻觉。|
| 伦理和隐私问题	| 从外部数据库存储和检索文本可能引起伦理和隐私方面的担忧。	| 训练数据中的敏感内容可能会引起伦理和隐私方面的问题。|
> **RAG 与微调**可以相互补充，而非相互排斥，从而在不同层次上增强模型的能力。


### RAG 框架
#### 原始 RAG
原始 RAG（Naive RAG）代表了早期研究方法，在 ChatGPT 广泛应用后迅速崭露头角。原始 RAG 的流程包括传统的索引、检索和生成步骤。

**索引**
离线状态下，从数据来源处获取数据并建立索引的过程。构建数据索引步骤如下：
1. 数据索引：清理和提取原始数据，将 PDF、HTML、WORD、Markdown 等不同格式的文件转换成纯文本。
2. 分块：将加载的文本分割成更小的片段。由于语言模型处理上下文的能力有限，因此需要将文本划分为尽可能小的块。
3. 嵌入和创建索引：这一阶段涉及通过语言模型将文本编码为向量的过程。所产生的向量将在后续的检索过程中用来计算其与问题向量之间的相似度。由于需要对大量文本进行编码，并在用户提问时实时编码问题，因此嵌入模型要求具有高速的推理能力，同时模型的参数规模不宜过大。完成嵌入之后，下一步是创建索引，将原始语料块和嵌入以键值对形式存储，以便于未来进行快速且频繁的搜索。

**检索**
根据用户的输入，采用与第一阶段相同的编码模型将查询内容转换为向量。系统会计算问题向量与语料库中文档块向量之间的相似性，并根据相似度水平选出最相关的前 K 个文档块作为当前问题的补充背景信息。

**生成**
将给定的问题与相关文档合并为一个新的提示信息。随后，LLM 被赋予根据提供的信息来回答问题的任务。根据不同任务的需求，可以选择让模型依赖自身的知识库或仅基于给定信息来回答问题。如果存在历史对话信息，也可以将其融入提示信息中，以支持多轮对话。

**Naive RAG 的挑战**
- 检索质量：低精度，即检索集中的文档块并不都与查询内容相关，这可能导致信息错误或不连贯。其次是低召回率问题，即未能检索到所有相关的文档块，使得大语言模型无法获取足够的背景信息来合成答案。此外，过时信息也是一个挑战，因为数据冗余或过时可能导致检索结果不准确。
- 回应生成质量：制造信息错误，即模型在缺乏足够上下文的情况下虚构答案。另一个问题是回答不相关，即模型生成的答案未能针对查询问题。进一步来说，生成有害或偏见性回应也是一个问题。
- 增强过程：首先是如何将检索到的文段的上下文有效融入当前的生成任务。如果处理不得当，生成的内容可能显得杂乱无章。当多个检索到的文段包含相似信息时，冗余和重复成为问题，这可能导致生成内容的重复。此外，如何判断多个检索到的文段对生成任务的重要性或相关性非常有挑战性，增强过程需要恰当地评估每个文段的价值。检索到的内容可能具有不同的写作风格或语调，增强过程需调和这些差异，以确保最终输出的一致性。最后，生成模型可能会过度依赖于增强信息，导致生成的内容仅是重复检索到的信息，而缺乏新的价值或综合信息。

#### 高级 RAG
为提升 Naive RAG，高级 RAG 进行了针对性的改进。

**优化数据索引**
优化数据索引旨在提高索引内容的质量。目前主要采用五种策略：提升索引数据粒度、优化索引结构、增加元数据、对齐优化以及混合检索。
- 提升数据粒度：提升文本的标准化和一致性。文本标准化意在剔除无关信息和特殊字符，提升检索效率。文本一致性意在消除术语和实体的歧义，剔除重复或冗余信息，简化检索过程。考虑时间敏感性，应更新过时文档。优化索引数据的重点在于清晰度、上下文和正确性，以提高系统的效率和可靠性。
- 优化索引结构
- 添加元数据信息
- 对齐优化
- 混合检索
  
- 滑动窗口
- 细粒度分割
- 元数据

**检索**
1. 预检索
2. 后检索
3. 检索流程优化

#### 模块化 RAG


组成部分
- 检索器
- 生成器
- 增强方法


模型评估
- 关键的评估指标和能力
- 自动评估框架


未来发展
- 垂直优化
- 水平扩展
- 技术堆栈及生态系统



### 参考
https://baoyu.io/translations/ai-paper/2312.10997-retrieval-augmented-generation-for-large-language-models-a-survey
https://zhuanlan.zhihu.com/p/46016518
https://w3.hihonor.com/weshare/community/#/topic-detail?topicId=927052297534803968
https://github.com/chatchat-space/Langchain-Chatchat
