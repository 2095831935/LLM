# LLM
## 基础知识

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

**RAG 与微调**可以相互补充，而非相互排斥，从而在不同层次上增强模型的能力。



发展模式
- 初级 RAG
- 高级 RAG
- 模块化 RAG


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
