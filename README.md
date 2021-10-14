# 比赛全周期试验记录
-------------------------------------------------------------
队友：AlvinAi96、Algor_Bruce <br>
整理者：Algor_Bruce <br>
记录整理模板参考：https://github.com/AlvinAi96/serverless_prediction#readme


该比赛项目分为三大模块：数据探索性分析、数据预处理(含特征工程)、模型(调参及融合)

# 一. EDA
后续补充

-------------------------------------------------------------

# 二. DataPreposss

### 2021.9.29
首次提交baselines
|编号|预处理手段|线下AUC测评分数|线上AUC分数|代码版本|数据版本|
|:--:|:--:|:--:|:--:|:--:|:--:|
|1|未考虑引入train_inter, 进行了PSI选择特征, 12折交叉验证|0.869422|0.87505447910|baseline_20210929_v1|mysub_20210929_2219_seed|

### 2021.9.30
|编号|预处理手段|线下AUC测评分数|线上AUC分数|代码版本|数据版本|
|:--:|:--:|:--:|:--:|:--:|:--:|
|1|未考虑train_public和train_inter分布差异，对两部分训练集合并处理，进行了PSI选择特征, (交叉验证)cv=12|0.803242 | 未提交 |mysub_20210930_2059_seed|
|2|未考虑train_public和train_inter分布差异, 根据特征重要性结果，删除了重要性排名靠后的若干变量，cv=10| 0.878252 | 0.88206691 |baseline_20210930_v1|mysub_20210930_2349_seed| 

### 2021.10.2
|编号|预处理手段|线下AUC测评分数|线上AUC分数|代码版本|数据版本|
|:--:|:--:|:--:|:--:|:--:|:--:|
|1|引入train_inter进行分析，并根据train_pulic预测train_inter，并从train_inter中选取满足条件的样本来扩充train_public数据集|0.983422|0.88547781|baseline_20211002_v1|mysub_20211002_2340_seed|

分析: 虽然成绩有所提高，排名也进了6%，但是线下分数远高于了线上分数，预测出现了过拟合。
      初步分析，应该是train_inter融合时，出现了信息泄露。 具体原因，后续进一步挖掘。  

### 2021.10.3
|编号|预处理手段|线下AUC测评分数|线上AUC分数|代码版本|数据版本|
|:--:|:--:|:--:|:--:|:--:|:--:|
|1|在昨天的基础上，添加了post_code的衍生变量|0.984907|0.87240526747|baseline_20211003_v1|mysub_20211003_1657_seed|

分析：过拟合现象依然严重，估计是训练集和测试集数据分布不一致导致的。

### 2021.10.8
|编号|预处理手段|线下AUC测评分数|线上AUC分数|代码版本|数据版本|
|:--:|:--:|:--:|:--:|:--:|:--:|
|1|简单加权融合了lgb/xgb/cat三个模型，保留了marriage/offspring特征|过拟合|0.88325465262|baseline_20211008_v1|mysub_20211008_2317_seed|
|2|简单加权融合了lgb/cat两个模型，删除了marriage/offspring特征|过拟合|0.87402803179|baseline_20211008_v1|mysub_20211008_2330_seed|
|3|只用了lgb模型, 删除了marriage/offspring特征|过拟合|0.86860578313|baseline_20211008_v1|mysub_20211008_2344_seed|

### 2021.10.9
|编号|预处理手段|线下AUC测评分数|线上AUC分数|代码版本|数据版本|
|:--:|:--:|:--:|:--:|:--:|:--:|
|1|在baseline_Singer脚本的基础上，进行了微调修改|未记录(过拟合严重)|0.88504523293|baselines_0.891(bsfdwdgs)|baseline_Singer|

### 2021.10.12
|编号|预处理手段|线下AUC测评分数|线上AUC分数|代码版本|数据版本|
|:--:|:--:|:--:|:--:|:--:|:--:|
|1|将train_inter中独有列，匹配到了train_public和test_public中去，然后再分类变量两两组合构建特征,并筛选|0.8846755683921235|0.87551312183|fea_eng_v1&model_lgb_v1|mysub_20211012_2059_seed|

分析：过拟合虽然解决了，但是分数提升效果并不明显；

### 2021.10.13
|编号|预处理手段|线下AUC测评分数|线上AUC分数|代码版本|数据版本|
|:--:|:--:|:--:|:--:|:--:|:--:|
|1|train_public+test_public+train_internet(按照阈值0.05和isDefalut<0.5的isDefault设置为0)|0.980976|**0.89088621**|baseline_20211010_v1|mysub_20211013_1110_seed|
|2|按照(1)的思路，但是修改了阈值0.05，每次从trian_inte中抽取符合条件的5000+个样本|0.924596|0.88052479155|baseline_20211013_v1|mysub_20211013_2136_seed|
|3|按照(1)的思路，但是修改了阈值0.05，每次从trian_inte中抽取符合条件的1000+个样本| |0.88037652694|baseline_20211013_v2|mysub_20211013_2210_seed|

分析：(1) 分数虽然上去了，但是又出现过拟合了, 分析原因是引入的train_inter样本数量过多, 带来大量噪音；
      在(2) 中调整了筛选的阈值，减少了从train_inter中引入的样本，并依然采用|1|中为标签思路，过拟合现象得到缓解，但分数一般，


### 2021.10.14
|编号|预处理手段|线下AUC测评分数|线上AUC分数|代码版本|数据版本|
|:--:|:--:|:--:|:--:|:--:|:--:|
|1|采用对抗验证思路，从train_inter中选出最像train_public的3888个样本，合并到训练集train_public中，未做特征工程|0.873438|0.87950567601|LogicJack_adv&baseline_20211014_v1|mysub_20211014_2336_seed|
|2|和(1)思路一样，只是修改了抽样阈值，选了2508个样本|0.876081|0.87935496748|LogicJack_adv&baseline_20211014_v1|mysub_20211014_2348_seed|
|3|同上，样本筛选了999个|0.876982|0.87607522392|LogicJack_adv&baseline_20211014_v1|mysub_20211014_2355_seed|

分析: 过拟合是解决了，还稍微有些欠拟合，线上分数掉了些

-------------------------------------------------------------

# 三. Models


后续补充



