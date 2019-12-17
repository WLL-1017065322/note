# article: 文章

- title(标题s) abstract（摘要s） compressed(精简显示s) image(配图s) author(作者s)
- source(来源s) pubDate(发布日期d)  labels(标签s) report(报告s) reportId(ref:Brief)
- readingCount(阅读量n) properties(属性s) avaliable(上架b) industry(行业名称)
- industryId(行业编号 a) paragraphs:(段落内容s) 



- isDeleted(b) 

- createdBy(s) createdByName(s) updatedBy(s)  updatedByName: (s)

- updatedAt(d) createdAt(d)

  

# attachment:报告附件数据模型

- section(ref:Section) brief(ref:Brief) title(s) _class(类型s)0图片1政策2Size3专家说

- startAt(引用此附件的位置) length(引用此附件的文本长度n)

- subObject:(Schema.Types.ObjectId)

  

-  isDeleted(b) ......



base

- ......



# brief:报告摘要数据模型

- title(大标题s) status(状态n) 0草稿-1退回修改1提交待审核2已经发布
- kind(n)_type(属性n)//0免费报告1普通报告 2？精品报告
- subTitle(副标题s) reportType(报告类型s |一级市场) 
- writtenDate(d) releaseDate(发布日期d)  version(版本号n)
- industry(行业名称s)  industryId(行业编号a) labels(报告标签s)
- PM(报告编撰项目经理s) personal(个人s) brandName(厂牌s)
- code(报告内部编号s) outline(报告提纲根目录 ref:outline)

# element: 报告幻灯片元素数据模型

- brief(ref:Brief) slide(ref:Slide) top: Number,
- left: Number width: Number height: Number title: String text: String
- attachment{_class,n;startAt,n;length,n;subObject}

# eon:报告名词解释数据模型

- brief(ref:brief) noun(名词s) explanation(s)

- isDeleted(b)......



# ill 报告插图数据模型

- title(标题s) senseless(无意义图片b) fileName(原始文件名s) source(图片来源s)
- hiImageUrl(高清图片URL s) loImageUrl(小图URL) OCRText(OCR文本s)
- isImageStory(是否图说标签b) 
- isDeleted(b)...



# individual 个人资料

- name () gender(微信性别 n)男、女、未知  description(s)
- avatar(头像Url s) teamIds(ref:team)

# outline 报告提纲数据模型

- name(s) brief(ref: 'Brief') parent(ref: 'Outline') subjects( ref: 'Outline')
- sections(ref: 'Section')   isEon(b) slide(ref: 'Slide')

# quote 报告专家说数据模型

- subject(主题s) expertId(内部系统中专家编号s) expertName(专家姓名 s)
- content(s) industry(行业名称 s)  industryId(行业编号 a) status(状态n)
- isDeleted(b)....

# section 报告段落正文数据模型

- text(s) brief(ref: 'Brief') outline(ref: 'Outline')
- attachment{_class(n) startAt(n) length(n) subObject()}



# size 报告Size数据模型

- title(标题s) description(描述s)





# sizePro



# slide



# table



# team

