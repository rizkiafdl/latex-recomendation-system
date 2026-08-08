TYPE Original Research PUBLISHED 03 October 2025 DOI 10.3389/feduc.2025.1673956 

##### OPEN ACCESS 

EDITED BY Sergio Ruiz-Viruel, University of Malaga, Spain 

REVIEWED BY Jae-Hoon Kim, Korea Maritime and Ocean University, Republic of Korea K. Bala, Bharath Institute of Higher Education and Research, India 

*CORRESPONDENCE Ling Jian bebetter@upc.edu.cn 

RECEIVED 27 July 2025 ACCEPTED 15 September 2025 PUBLISHED 03 October 2025 

###### CITATION 

Wang X, Zhou J, Jian L, Yin Y and Li L (2025) Empowering college students to select ideal advisors: a text-based recommendation model. _Front. Educ._ 10:1673956. doi: 10.3389/feduc.2025.1673956 

###### COPYRIGHT 

© 2025 Wang, Zhou, Jian, Yin and Li. This is an open-access article distributed under the terms of the Creative Commons Attribution License (CC BY). The use, distribution or reproduction in other forums is permitted, provided the original author(s) and the copyright owner(s) are credited and that the original publication in this journal is cited, in accordance with accepted academic practice. No use, distribution or reproduction is permitted which does not comply with these terms. 

# Empowering college students to select ideal advisors: a text-based recommendation model 

Xinmin Wang<sup>1</sup> , Jiaxin Zhou<sup>2</sup> , Ling Jian<sup>1</sup> *, Yue Yin<sup>1</sup> and Li Li<sup>3</sup> 

1School of Economics and Management, China University of Petroleum, Qingdao, China, 2School of Science, China University of Petroleum, Qingdao, China,<sup>3</sup> Information Construction Division, China University of Petroleum, Qingdao, China 

College students often encounter numerous challenges throughout their academic journeys, making the guidance and support from educators indispensable. Recommendation systems can significantly reduce the difficulties students face when identifying suitable academic advisors. This paper proposes a AdVisor RecommenDation (AVRD) model based on textual data regarding college students’ interests. AVRD first adopts Chinese Bidirectional Encoder Representations from Transformers (BERT) and unsupervised Simple Contrastive Learning of Sentence Embeddings (SimCSE) to train the corpus of advisors’ records. The time decay factor is then introduced as the weight of the text record vectors, and the representation vectors of advisors are obtained using the weighted mean. Finally, the similarities between the advisor and student vectors are computed, and an advisor list is recommended to student according to the designed pooling and matching criteria. The questionnaire data from 170 college students are collected to evaluate the proposed model. Experimental results demonstrate the effectiveness of AVRD. The model outperforms other LLMs such as Qwen and DeepSeek by a significant margin, as well as the commonly used models like TF-IDF, LSA, and Word2Vec. Moreover, the ablation studies reveal that the SimCSE component of AVRD is crucial to the model’s performance. 

###### KEYWORDS 

advisor recommendation, text-based recommendation, bidirectional encoder representations from transformers, simple contrastive learning of sentence embeddings, large language model 

## 1 Introduction 

College students face substantial societal expectations for cultivating multifaceted competencies, including academic excellence, personal development, and professional proficiency. In striving to achieve these goals, educators play an indispensable role in assisting students to overcome diverse challenges they may encounter by providing various forms of support (Goldberg et al., 2023; Christoforidou and Kyriakides, 2021). For instance, when students are engaging in high-level competitions like the _Mathematical Contest in Modeling_ (MCM) and the _Interdisciplinary Contest in Modeling_ (ICM), the invaluable guidance from experienced advisors can prove instrumental in maximizing students’ success in these demanding competitions, particularly in enhancing their competition completion rate and award-winning opportunities (Wankat, 2005). As another illustration of the importance of an advisor, when students embark on their graduation projects, a proficient advisor can play a pivotal role in cultivating their academic interests and enhancing their academic performance, laying a solid foundation for their future academic research (Pargett, 2011). 

frontiersin.org 

Frontiers in Education 

10.3389/feduc.2025.1673956 

Wang et al. 

For a university or school with a limited number of faculty members, students can rapidly identify an ideal advisor whose expertise and guidance closely align with their specific needs through online searches or peer recommendations. However, as the number of teachers increases, this process becomes increasingly challenging (Gordon and Steele, 2015). First, students might not have well-defined expectations regarding what they can expect from an advisor, making it challenging to identify an advisor who can meet their specific requirements. Second, there is a considerable and persistent information asymmetry between students and teachers. Typically, students often have limited access to in-depth details regarding potential advisors’ specialized knowledge, instructional approaches, and research foci. Despite the fact that school websites usually offer comprehensive teacher profiles, students often lack the necessary experience and acumen to discern the subtle differences in various teachers’ competencies and the specific support each can offer. This limitation significantly hinders their capacity to select the most appropriate advisor from a multitude of choices when in need of guidance. Third, students occasionally select advisors based on recommendations and experiences shared by familiar peers or teachers. While this practice can mitigate information asymmetry and broaden their options, it may also introduce bias into the selection process. This is because the recommended advisors might not always match the individual demands and preferences of the students seeking guidance. Finally, the search for an advisor can be a timeconsuming endeavor, and students, burdened by their academic obligations, often have scarce time to devote to this crucial task. Given the reasons outlined above, helping students identify their ideal advisor in an efficient way is a task of importance and necessity. 

To provide students with an advisor recommendation list for various competitions, paper writing, graduation projects, and other academic activities, this paper collects detailed data containing teacher profiles and students’ actual needs for selecting advisors from a China’s university and constructs an advisor recommendation model. The research objective is achieved by addressing the following four questions: (1) How to collect a validation set (matching records of text data between teachers and students) to evaluate model performance? (2) Which representation learning method works better in converting the texts of corpus into vectors? (3) How to properly integrate the teacher’s text information, which contains different types and different periods, to generate the teacher representation vector? (4) How to use text similarity to recommend advisors with satisfactory accuracy? 

To address these issues, we use text data, encompassing paper titles, course names, and teachers’ research fields, as the corpus. In view of the fact that deep learning technology has demonstrated promising performance in the field of recommendation (Wu et al., 2023), we combine Chinese bidirectional encoder representations from Transformers (BERT) (Cui et al., 2021) with the unsupervised simple contrastive learning of sentence embeddings (SimCSE) (Gao et al., 2021) models to extract text features and train sentence vectors. For each teacher, we extract the representation vectors from the text records by using the time decay factor to handle the impact of research interests shift. By issuing questionnaires to 

students, we collected the short texts on students’ demands for advisors and extracted the representation vectors of each student. Then, the cosine similarities between the vectors of teachers and students are calculated, and the pooling criterion and matching criterion are used to recommend an advisor list to each student. The validation set is the filtered questionnaire data filled out by the students, including students’ demand-related short texts and their corresponding lists of ideal advisors. To enhance the accuracy of the ground truth, we employ the large language model (LLM) DeepSeek (DeepSeek-AI, 2024a) to generate teacher profiles. These profiles are then provided to questionnaire respondents for a second round of adjustments, with the goal of reducing biases resulting from information asymmetry. 

This study has made the following major contributions: 

(i) We propose an advisor recommendation model AVRD, which can assist students to identify advisors using demand-related short texts. 

(ii) AVRD employs Chinese BERT and a contrastive learning model SimCSE to learn effective representations of texts. This approach of combing BERT with SimCSE can significantly enhance the performance of text similarity representation. 

(iii) The time decay factor is adopted to learn the text record vectors of teachers, enabling the model to better capture the features of their recent achievements. Advisor recommendation criteria, i.e., the pooling and matching criteria, are designed for the advisor recommendation task. Both the time decay factor and recommendation criteria can effectively improve the 

## 2 Related work 

### 2.1 Recommendations for education 

In the field of education, information technology is increasingly being applied to help students, teachers, and administrators improve work efficiency, information construction has played a pivotal role in higher education improvement and teaching management (Yu, 2022; Lee, 2017). Information systems collect a large amount of campus data, including basic information and behavioral data of students and teachers. Based on these data, scientific researchers conduct research using data mining and machine learning theories and techniques to explore teaching suggestions and improve teaching quality. 

Academic prediction and warning is one of the important research directions. Cui et al. (2022) and Yang et al. (2020) transformed behavior trajectory data of students and textural logs of records into learning images and trained images through convolutional neural network (CNN) variant to provide warnings of problems in the academic performances of students. Imran et al. (2019) and Zou et al. (2025) utilized machine learning approaches to predict student academic performance. Oztas and Akcapinar (2025) predicted students’ academic procrastination tendencies using online learning trajectories. Besides the prediction tasks, machine learning is more commonly used for recommendations, such as course recommendations, major recommendations, etc. Wang X. et al. (2022) and Zhang et al. (2019) proposed massive 

frontiersin.org 

Frontiers in Education 

10.3389/feduc.2025.1673956 

Wang et al. 

open online course (MOOC) recommendation algorithms based on hyperedge-based graph neural networks and hierarchical reinforcement learning respectively. Wang et al. (2020) proposed an attention-based CNN to predict user ratings and recommend top-ranked courses to users. Regarding major recommendation, Obeid et al. (2022) collected the life trajectory information of graduates via questionnaires and then proposed a hybrid major recommendation system that incorporates knowledge base and collaborative filtering techniques. Alghamdi et al. (2019) employed a fuzzy recommender system and random forest algorithm to facilitate personalized major recommendations for students. 

The aforementioned studies contribute to enhancing students’ learning effectiveness through various approaches, including grade prediction, resource recommendation, major recommendation, and more. Teacher recommendation is also very important for college students, when seeking guidance from teachers for graduation projects, professional competitions, or paper writing. In the field teacher recommendation, Zhang et al. (2016) proposed a teacher recommendation model using the term frequency-inverse document frequency (TF-IDF) method, this model conducts recommendations by obtaining the social information between students and teachers within the school and calculating the connectivity, therefore, it is more suitable for recommending supervisors to the group of graduate students enrolled in undergraduate schools. Zemaityte and Terzi´c (2019) designed a teacher recommendation tool for computer science students through calculating the TF-IDF scores to rank supervisors as well. Both studies assessed the recommendation performance of their models from the perspective of user satisfaction, which differs from the accuracy calculated based on the ground truth datasets, and they assume the mutual independence of words without considering their semantic meaning in the models. In our work, we construct a ground-truth dataset for advisor selection to evaluate the recommendation performance, and integrate the semantic meaning of words by leveraging the BERT model. 

### 2.2 Recommender systems and algorithms 

The recommender systems are software tools and techniques that provide suggestions for items that are most likely of interest to a particular user (Ricci et al., 2022), which are increasingly being applied in the field of education. Based on the online review data of MOOC users, Nilashi et al. (2022) proposed a multi-criteria collaborative course recommender system through machine learning techniques. Wu and Feng (2020) proposed an improved neural network-path sorting algorithm to improve the recommendation efficiency of online education resources. Ma et al. (2017) applied course recommendations in the curriculum system by analyzing courses with similar semantics. The core algorithms of these recommender systems are information filtering approaches. At present, collaborative filtering and content-based filtering are two major approaches to constructing recommender systems. Collaborative filtering leverages the key source of useritem interaction data, whereas content enriched recommendation additionally utilizes the side information associated with users and items (Wu et al., 2023). 

Content-based filtering extracts item features from users’ usage records or item profiles and then recommends items based on similarity (Esmeli et al., 2020). Zhang et al. (2016) used the modified TF-IDF algorithm and category tree to determine the direct similarity relationship between students and teachers. Zemaityte and Terzi´c (2019) extracted two lists of keywords from the crawled text data of potential teachers and the demand text provided by students respectively, then ranked and matched the two keyword lists using TF-IDF algorithm as well. These two teacher recommendation approaches adopt content-based filtering, which extracts features of students and teachers through similarity calculation. Inspired by the remarkable achievements of deep learning, deep neural networks have demonstrated superior performance in text feature extraction and are widely employed in content-based filtering recommendation algorithms. For example, Thierry et al. (2023) proposed a paper recommendation model using knowledge graph embedding and deep neural network, in which introduced an attention module to strengthen the semantic feature extraction and learns enhanced representations. 

In addition to the field of education, there are also many researches in other recommendation fields based on deep learning. Guo et al. (2017) interacted low-order and high-order features through deep learning and proposed the deep factorization machine (DeepFM) model to predict the click-through rate (CTR) of users in the recommendation system. Fan et al. (2019) proposed the graph neural network framework GraphRec to predict recommendation item scores for users, the model captured the expression vectors of major user features and major item features through the attention mechanism. Wang Z. et al. (2022) proposed the PJFCANN model for job recommendation using recurrent neural network (RNN) to extract textual features from job applicants’ resumes and recruiters’ demands, while graph neural network (GNN) was used to extract features of recruiters in their historical experiences. These recommendation algorithms all need to train model parameters using a substantial amount of historical interaction data of users and items, whereas the commonly used recommendation models that perform text filtering based on semantic similarity such as those using TF-IDF and Latent Semantic Analysis (LSA) (Evangelopoulos et al., 2012) only extract the text features of paired records and don’t need to train the model’s parameters. Considering the demonstrated potential and efficacy of deep learning in the realm of recommendation systems, we employ a content-based filtering approach using deep neural network to facilitate advisor recommendation. 

## 3 Methodology 

The proposed AVRD model involves two key entities: universities and students. Universities provide updated teacher text data, which serves as the corpus for model processing. Students input their “demand-related short texts” into the model and offer feedback on the recommended teacher lists. The flowchart of advisor recommendation is shown in Figure 1. 

Before the advisor recommendation algorithm can be deployed as an information system, the underlying recommendation model must be fully pre-trained. Although this pre-training process is computationally intensive and time-consuming, it ensures 

frontiersin.org 

Frontiers in Education 

10.3389/feduc.2025.1673956 

Wang et al. 

### 3.2 Vector representation 

In our work, teachers’ text data contain three kinds of records, i.e., course records ( _T_<sup>_C_</sup> ), research direction records ( _T_<sup>_D_</sup> ), and paper publication records ( _T_<sup>_P_</sup> ). Suppose that the total number of teachers is _J_ , and the three types of text records of teacher _j_ are denoted by _Tj_<sup>_C_,</sup><sup>_T_</sup> _j_<sup>_D_and</sup><sup>_T_</sup> _j_<sup>_P_respectively,</sup><sup>_j_∈</sup><sup>_J_={1, 2,</sup><sup>_. . ._,</sup><sup>_J_}.Thenthe</sup> vectors **r**<sup>_C_</sup> _j_<sup>,</sup><sup>**r**</sup><sup>_D_</sup> _j_<sup>, and</sup><sup>**r**</sup><sup>_P_</sup> _j_<sup>are extracted from the corresponding three</sup> records of teacher _j_ . Therefore, the representation vector of teacher _j_ is **r** _j_ = [ **r**<sup>_C_</sup> _j_<sup>,</sup><sup>**r**</sup><sup>_D_</sup> _j_<sup>,</sup><sup>**r**</sup><sup>_P_</sup> _j_<sup>], which is the concatenation of</sup><sup>**r**</sup><sup>_C_</sup> _j_<sup>,</sup><sup>**r**</sup><sup>_D_</sup> _j_<sup>, and</sup><sup>**r**</sup><sup>_P_</sup> _j_<sup>.</sup> Take the paper publication records _Tj_<sup>_P_of teachers</sup><sup>_j_for example,</sup> we introduce the calculation process of the vector **r**<sup>_P_</sup> _j_<sup>.Let</sup><sup>_t_</sup> _ji_<sup>_P_</sup> denotes the _j_ th teacher’s _i_ th paper publication record, all the paper publication records of teacher _j_ is denoted as follows: 

where _m_<sup>_P_</sup> _j_<sup>is the number of paper publication records of teacher</sup><sup>_j_.</sup> According to Equation 3, the representation vector of record _tji_<sup>_P_is</sup> **c**<sup>_Pj_</sup> _i_<sup>, where</sup><sup>_i_= 1, 2,</sup><sup>_. . ._,</sup><sup>_mP_</sup> _j_<sup>.</sup> 

Since the generated time of each paper publication record is different, we introduce the time decay factor _η_<sup>_λi_</sup> to emphasize the recent work of teachers, where _η_ is a constant between 0 and 1, and _λi_ , _i_ = 1, 2, _. . ._ , _m_<sup>_D_</sup> _j_<sup>,isapositiveintegerrelatedtothe</sup> generated time of corresponding record. Taking the natural year as the interval, we set _λ_ = 1 for records from the most recent year, _λ_ = 2 for the second most recent year, and so on. At this time, the paper publication vector **r**<sup>_P_</sup> _j_<sup>of teacher</sup><sup>_j_is computed as the weighted</sup> average of the representation vector **c**<sup>_Dj_</sup> _i_ corresponding to each paper publication record, where the time decay factor _η_<sup>_λi_</sup> serves as the weighting coefficient. It can be expressed by the formula as follows: 

As the query texts for students in the advisor recommendation system are non-deterministic, we set three types of demand as the query texts, which are course names ( _�_<sup>_C_</sup> ), research directions ( _�_<sup>_D_</sup> ), and project keywords ( _�_<sup>_P_</sup> ) related to the objectives of the activities or projects, this setting is analogous to the records used for teachers. After removing words that are not in the model corpus from these text records, we represent students using the same method as teacher vector representation. The representation vector of student _e_ is defined as **u** _e_ = [ **u**<sup>_C_</sup> _e_<sup>,</sup><sup>**u**</sup><sup>_D_</sup> _e_<sup>,</sup><sup>**u**</sup><sup>_P_</sup> _e_<sup>].</sup> 

### 3.3 Recommendation prediction 

After the vector representation is obtained, some methods can be adopted to calculate the similarity between vectors, such as dot product, Euclidean distance, and cosine similarity (Manning et al., 2008). The values of dot product and Euclidean distance are susceptible to dimensional influence, with non-fixed ranges. However, even in the case of high dimensions, the cosine similarity still keeps the excellent property of “the same is 1, the orthogonal 

is 0, and the opposite is -1”. Hence, in the fields of information retrieval and text mining, cosine similarity is widely used to measure document similarity (Kiri¸sci, 2023; Luo et al., 2018), as noted by Thongtan et al. (2019). Therefore, we adopt the cosine similarity _s_ **u** _e_ , **r** _j_ in Equation 2 as the metric between student vector **u** _e_ ( _e_ ∈ _E_ = {1, 2, _. . ._ , _L_ }) and teacher vector **r** _j_ ( _j_ ∈ _J_ ). 

Moreover, in the recommendation prediction module, we propose a pooling criterion and a matching criterion. A list of potential advisors is determined according to the pooling criterion firstly, which is then supplemented based on the matching criterion. 

Three types of similarity can be computed based on **u**<sup>_C_</sup> _e_<sup>,</sup><sup>**u**</sup><sup>_D_</sup> _e_<sup>,</sup><sup>**u**</sup><sup>_P_</sup> _e_ between a student and a teacher. The pooling criterion selects the maximum output; specifically, the similarity score of the teacher record that best aligns with the student demand is adopted as the representative similarity between the teacher and the student. This approach filters out redundant similarity information and significantly reduces subsequent computations. Taking the input text _�e_<sup>_D_of student</sup><sup>_e_as an example, the similarity</sup><sup>_s_</sup> **u**<sup>_D_</sup> _e_ , **r** _j_<sup>with teacher</sup> _j_ is: 

i.e., the maximum value of the similarity set between its representation vector **u**<sup>_D_</sup> _e_<sup>andtheteacher’sthreerepresentation</sup> vectors **r**<sup>_C_</sup> _j_<sup>,</sup><sup>**r**</sup><sup>_D_</sup> _j_ and **r**<sup>_P_</sup> _j_<sup>.AccordingtoEquation 6,weobtainthe</sup> similarity set { _s_ **u** _De_ , **r** 1 , _. . ._ , _s_ **u** _De_ , **r** _j_ , _. . ._ , _s_ **u** _De_ , **r** _k_ } between input text _�e_<sup>_D_</sup> of user _e_ and _J_ teachers, and the same principle holds for _�e_<sup>_C_and</sup> _�e_<sup>_P_.Inthismanner,the</sup><sup>_n_maximumvaluesareextractedfrom</sup> each of the three sets, each containing _J_ similarity values, to form a new similarity set with a length of 3 _n_ . Subsequently, a list of recommended teachers is determined based on the frequency of teachers’ occurrences and their similarity values in the new set. 

The list of teachers with the highest similarity to students’ demands can be generated based on the aforementioned pooling criterion. To tackle the problem of incomplete teacher selection when relying solely on similarity, we propose a matching criterion that first evaluates the text inclusion relationship and then recommends teachers based on similarity scores. However, the matching criterion may recommend teachers with similarity scores significantly lower than students’ demands. Additionally, when the student’s demand text contains an excessive number of words, no advisor may be recommended due to the absence of inclusion relations. Therefore, the teacher recommendation list generated by the matching criterion serves as a supplement to that derived from the pooling criterion. The matching criterion assesses whether the word sequences of teachers (i.e., _Tj_<sup>_C_,</sup><sup>_T_</sup> _j_<sup>_D_and</sup><sup>_T_</sup> _j_<sup>_P_) contain the word</sup> sequences of student _e_ (i.e., _�e_<sup>_C_,</sup><sup>_�_</sup> _e_<sup>_D_and</sup><sup>_�_</sup> _e_<sup>_P_), and selects the set</sup><sup>_T_′</sup> of all the teachers whose word sequences include any of student _e_ ’s word sequences. Subsequently, the similarity between the teacher vector represented in _T_<sup>′</sup> and the corresponding vector of student _e_ is calculated. Advisors are then recommended based on both the frequency of their occurrence in the similarity sequence and their similarity scores. 

Recommendation prediction is performed by applying a pretrained AVRD model. This model is typically saved as a file that contains both the BERT-based network architecture and the associated parameter values, including weights and biases. To deploy a recommendation system using the pre-trained model, 

frontiersin.org 

Frontiers in Education 

10.3389/feduc.2025.1673956 

Wang et al. 

we can begin by computing vector representations for all teachers and storing them in a database. Upon receiving a student inquiry, the server uses the pre-trained model to generate a vector for the input text. It then produces a ranked list of recommended advisors by calculating and sorting the similarity scores between the inquiry vector and all pre-computed teacher vectors in the database. Johnson et al. (2019) demonstrated in their experiment that retrieving the top-100 similarities from a dataset of 10,000 1024-dimensional vectors requires less than 10 ms. This experiment was performed on a system configured with two 2.8 GHz Intel Xeon E5-2680v2 CPUs, four Maxwell Titan X GPUs, and CUDA 8.0. Consequently, with proper server deployment, the advisor recommendation system can operate at a sufficiently fast and satisfactory speed. 

TABLE 1 Teacher dataset. 

|~~Record type~~|~~Field name~~|~~Number~~|
|---|---|---|
|Course records|Teacher ID|2,324|
||Course name|68,792|
||Year of teaching|68,792|
|Research direction records|Teacher ID|1,970|
||~~Research direction~~|~~1,970~~|
||School|1,882|
|Paper publication records|Teacher ID|2,248|
||Paper title|28,754|
||Year of publication|28,754|
||School|1,700|

## 4 Experimental results 

Before being deployed as a recommendation system, recommendation algorithms are typically fully trained using large-scale datasets. The recommendation efficiency of the system depends entirely on the training performance. First and foremost, the quality of the dataset serves as the foundation, as all welltrained algorithms rely on high-quality ground truth. Meanwhile, a set of evaluation metrics is typically employed to compare the recommendation performance across classic algorithms. 

This section starts with an introduction to the collection, processing, and testing of experimental data. Subsequently, it analyzes the recommendation results of the proposed model, baseline models, and ablation models on the validation dataset. Finally, t-distributed stochastic neighbor embedding (t-SNE) (Maaten and Hinton, 2008) is employed to transform the text representation vectors of teachers into two-dimensional scatter plots, which are then analyzed in depth based on different schools. 

### 4.1 Datasets 

In this paper, experimental data include information systemcollected data and questionnaire survey data. Information system data are employed for text feature extraction and vectorized representation of teacher-related texts. Questionnaire survey data, meanwhile, include students’ short texts describing their demands as well as the corresponding lists of target teachers. Based on the questionnaire survey data, we constructed the validation set for the advisor recommendation model and realized the vectorized representation of students’ demands. The sources and processing methods of these two types of data are described in the following sections. 

We collected 30,458 text records, including 4,067 course records, 1,970 research direction records, and 24,421 paper publication records in total. Following text length screening, we constructed a corpus consisting of 24,374 paper publication word sequences, 3,762 course word sequences, and 1,577 research direction word sequences; the length of remaining sentences uniformly aligned to 40. Subsequently, we used these 29,713 sentences to fine-tune the Chinese BERT (RoBERTa-wwm-ext) by applying the masked language model objective (Cui et al., 2021). 

#### 4.1.1 Teacher dataset 

Teachers’ text information is sourced from the Information Construction Department of a Chinese university, with abundant teacher data collected over the years. Specifically, academic research-related textual data primarily include course records, research direction records, and Chinese-language paper publication records. The research direction records comprise direction information uploaded by teachers and the names of corresponding primary and secondary disciplines, which clearly and systematically detail their research interests. The course records list all courses taught by individual teachers, reflecting their specialized competencies. The earliest course record dates back to 2004, with records covering the period up to 2021. The paper publication records include the titles of all papers authored by teachers, among which the most recent publication reflects their latest research endeavors. Although the earliest publication record traces back to 1970, the majority of documents were published between 1999 and 2021. A statistical summary of all three data types is presented in Table 1. 

Owing to the varying frequencies of textual records and differences in the number of teachers across data categories, significant disparities exist in the quantities of course records, research direction records, and paper publication records. For many teachers, course records tend to accumulate and recur as their teaching experience grows. Initially, there were 127,232 course records, covering 4,067 courses and 2,324 teachers. After removing annual duplicate course records for each teacher, 68,792 course records remained. The other two types of records were subsequently cleaned and organized. The research direction data include records for 1,970 teachers, while the publication data contain 28,754 records from 2,248 teachers, involving 24,421 papers. Finally, 1,921 teachers with course records from the past three years were selected as potential recommendable candidates, excluding those who had resigned or retired. 

#### 4.1.2 Student dataset 

Students’ demand data were collected via a questionnaire survey, with respondents being students from the same university whose teacher data are used for text vector representation learning. 

frontiersin.org 

Frontiers in Education 

10.3389/feduc.2025.1673956 

Wang et al. 

with a batch size of 64, adopting the AdamW optimizer (learning rate = 1e-5) and a dropout rate of 0.3. 

For vector representation, each record is encoded as a vector using the [CLS] token from the last hidden state of BERT. With the time decay factor of _η_ = 0.88, the teacher’s representation vector is computed via the weighted average of records, as shown in Equation 5. Student representation vectors are derived from three types of questionnaire items: two items regarding preferred course names, three items about interested research fields, and three items related to project keywords. Ultimately, three 768dimensional teacher vectors and three 768-dimensional student vectors are obtained. 

We set the length of the recommendation list to 10 or 20, meaning that 10 or 20 teachers from 1,921 candidates are first recommended to each of the 170 students according to the pooling criterion. Subsequently, an additional 2 teachers are recommended to each student according to the matching criterion. In practice, when pooling and matching criteria are applied simultaneously, the length of the recommendation list may not exceed 10 or 20 due to potential overlaps among recommended advisors. When applying the pooling criterion, the top 15 maximum values are extracted from each of the three sets (each containing 1,921 similarity values) to form a new similarity set of length 45, which is then used to generate the final recommendation list. For the matching criterion, each text record is segmented using the Jieba word segmentation tool. Stopwords are selected from the _Baidu_ Stopwords List and the Stopwords List of _Harbin Institute of Technology_ . 

### 4.3 Model performance 

#### 4.3.1 Evaluation metrics 

For evaluation metrics of the recommendation system, Liu et al. (2023) used the hit rate (HR) index to assess the system’s performance, Huang et al. (2021) adopted the HR index and NDCG (Normalized Discounted Cumulative Gain) index. The HR index (Deshpande and Karypis, 2004) calculates the recall rate of the entire recommendation system by averaging the recall rates of all users. The NDCG index (Zhang et al., 2023; Karthik and Ganapathy, 2021) is a measurement standard that considers user scores and recommendation order. Gu et al. (2020) utilized recall rate, precision and F1 value. Additionally, mean reciprocal rank (MRR) and mean average precision (MAP) are frequently used metrics to measure item order in recommendation lists (Gu et al., 2022). 

In this paper, we select the two commonly used metrics, MAP and HR, to evaluate the performance of our advisor recommendation model. MAP and HR can be regarded as the average probability of correctly recommending each advisor in the ground truth. To investigate the proportion of students whose demands are satisfied, we additionally propose two new metrics: I-REA and II-REA. I-REA is defined as the ratio of students for whom at least one advisor is successfully recommended, while IIREA is the ratio of students for whom at least two advisors are successfully recommended. In fact, the four selected metrics reflect two perspectives for evaluating recommendation performance. 

@ _K_ denotes the length of recommendation list. R _e_ @ _K_ represents the set of recommended teachers for student _e_ , while T _e_ is the ground-truth set of student _e_ . | · | denotes the cardinality (i.e., the number of elements) of a set. 

#### 4.3.2 Comparative evaluation on AVRD 

We conducted experiments to evaluate the performance differences between the proposed advisor recommendation model (AVRD) and several baseline vector representation methods. The following four algorithms are commonly employed recommendation models that leverage vector similarity. A brief introduction to each is provided below: 

- **TF-IDF:** TF-IDF is computed by multiplying a local component (term frequency) with a global component [inverse document frequency (Jones, 1972)], followed by normalizing the resulting document vectors to unit length. 

- **LSA:** In LSA model (Deerwester et al., 1990), singular value decomposition (SVD) is applied to the term-document matrix, and the first 300 dimensions are retained as text vectors. In our experiment, we used SVD on the term-document matrix of the teacher corpus to capture the latent semantics of words, with the number of factors (latent dimensions) set to 480. 

- **LSA+TF-IDF:** The word frequencies in the word-document matrix are replaced by the TF-IDF values of the words, and SVD is applied to the matrix (Li and Shen, 2017). In our experiment, the number of factors (latent dimensions) was set to 430. 

- **Word2Vec:** We employed the Skip-Gram algorithm (Mikolov et al., 2013a) with hierarchial softmax output layer (Morin and Bengio, 2005) to represent each word as a 340-dimensional dense vector. During training, the minimum word occurrence frequency was set to 1, effectively preserving infrequent professional terms. Additionally, the window size for each movement is set to 7. We used the same text tokenization tool and stopwords list as those applied in the matching criteria. The corpus contained a total of 22,237 valid and non-repetitive terms. 

The performance of each model is presented in Table 2. Regarding all four evaluation metrics, our AVRD model outperforms the other four models. Specifically, the I-REA index based on the top-10 recommendations is 0.8059, indicating that the demands of more than 80% students (170 in all) can be met. When recommending 20 advisors, this rate increases to 

frontiersin.org 

Frontiers in Education 

10.3389/feduc.2025.1673956 

Wang et al. 

TABLE 2 Performance of the AVRD model and baselines. 

|Top-K||Top|-10|||Top|-20||
|---|---|---|---|---|---|---|---|---|
|Methods|I-REA|II-REA|MAP|HR|I-REA|II-REA|MAP|HR|
|TF-IDF|0.6765|0.4059|0.4438|0.4118|0.8118|0.6471|0.4891|0.5784|
|~~LSA~~|~~0.4176~~|~~0.1588~~|~~0.1753~~|~~0.2078~~|~~0.5765~~|~~0.3059~~|~~0.2054~~|~~0.3333~~|
|LSA+TF-IDF|0.5882|0.2824|0.3444|0.3353|0.7647|0.4706|0.3811|0.4843|
|Word2Vec|0.5824|0.2647|0.2493|0.3098|0.7235|0.4706|0.2944|0.4588|
|**AVRD**|**0.8059**|**0.5412**|**0.5178**|**0.5118**|**0.9000**|**0.7529**|**0.5825**|**0.7000**|

The bold values indicate the best results among all methods. 

90%. The II-REA index stands at 0.5412, indicating that over half of the students can receive at least two ideal recommendations. With an HR of 0.5118, our AVRD model, on average, can provide each student with at least one ideal advisor when generating a recommendation list of 10 advisors. 

As discussed in Section 3, our AVRD model is essentially a fine-tuned BERT architecture. A natural follow-up question is: can other fine-tuned LLMs outperform fine-tuned BERT in the task of advisor recommendation? To further compare the performance of our AVRD model with that of other LLMs, we selected the Qwen2.5-7b (Qwen team, 2024) and DeepSeek-llm-7b-chat (DeepSeek-AI, 2024b) models for the advisor recommendation task. The results are shown in Table 3. “Without SimCSE” denotes recommendations made directly with the [CLS] representations of the three LLMs, without undergoing SimCSE training. For the Qwen(with SimCSE) model, we adopted an attention network to be the training model for SimCSE, using the [CLS] token of Qwen2.5-7b as the input to the attention network. Due to the high computational cost associated with full-parameter training of Qwen, we did not use Qwen2.5-7b as the training model for SimCSE. As shown in Table 3, the I-REA scores of Qwen, DeepSeek and AVRD without SimCSE are 0.2294, 0.2706 and 0.4118 respectively. In the advisor recommendation task, the sentence representation effect of the Chinese BERT model (RoBERTa-wwm-ext) outperforms that of Qwen and DeepSeek— this advantage is consistent with the II-REA, MAP, and HR metrics. The application of the SimCSE process leads to a substantial boost in recommendation performance. Specifically, the I-REA scores for Qwen and AVRD stand at 0.4824 and 0.8059, respectively. Notably, the improvements in II-REA, MAP, and HR are even more pronounced. The RoBERTa model removes the next sentence prediction objective and dynamically changes the masking pattern applied to the training data (Cui et al., 2021), therefore, it exhibits stronger sentence representation capabilities compared to models focused on text generation. In contrast, LLMs such as Qwen and DeepSeek place greater emphasis on long-text generation. For semantic textual similarity (STS) tasks such as advisor recommendation, the RoBERTa model shows superior performance, as shown in Table 3. 

#### 4.3.3 Ablation study 

Ablation experiments are conducted to evaluate the effectiveness of each module in our AVRD model. Three 

TABLE 3 Comparison of performance among LLMs. 

|~~Methods~~|~~I-REA~~|~~II-REA~~|~~MAP~~|~~HR~~|
|---|---|---|---|---|
|Qwen (without SimCSE)|0.2294|0.0235|0.0648|0.0843|
|DeepSeek (without SimCSE)|0.2706|0.0294|0.0874|0.1000|
|AVRD (without SimCSE)|0.4118|0.1176|0.1349|0.1824|
|Qwen (with SimCSE)|0.4824|0.2235|0.2178|0.2529|
|AVRD (with SimCSE)|0.8059|0.5412|0.5178|0.5118|

The scores are based on top-10 recommendations. 

TABLE 4 Performance of variants of the AVRD model. 

|~~Methods~~|~~I-REA~~|~~II-REA~~|~~MAP~~|~~HR~~|
|---|---|---|---|---|
|AVRD-S|0.4118|0.1176|0.1349|0.1824|
|~~AVRD-PM~~|~~0.7118~~|~~0.4118~~|~~0.4756~~|~~0.4157~~|
|AVRD-T|0.7882|**0.5706**|0.5131|**0.5176**|
|AVRD|**0.8059**|0.5412|**0.5178**|0.5118|

The scores are based on top-10 recommendations, and the time-decay rate is set to 0.88. The bold values indicate the best results among all methods. 

- **AVRD-S:** This variant of the AVRD model makes recommendations without considering the SimCSE training process. 

- **AVRD-PM:** This variant utilizes all modules of the AVRD model except for the pooling and matching criterion. Recommendations are generated by computing the similarities between the combined vectors of student text representations and teacher record representations. 

- **AVRD-T:** This variant utilizes all modules of the AVRD model without considering the time decay factor. 

As shown in Table 4, the scores of the AVRD-S model are significantly lower than those of the AVRD-PM, AVRD-T, and AVRD models, which demonstrates the effectiveness of the SimCSE module. The pooling and matching criteria play important roles in the AVRD model. After implementing the pooling and matching criteria, the I-REA score increased from 0.7118 to 0.8058, representing a 13.2% enhancement. Simultaneously, improvements are also observed in the II-REA, MAP, and HR metrics. The timedecay rate demonstrates a slight improvement effect, yet it shows no impact on the II-REA, MAP, and HR metrics.This can be attributed to the fact that teachers’ research interests and teaching courses do 

frontiersin.org 

Frontiers in Education 

10.3389/feduc.2025.1673956 

Wang et al. 

on the left. Data validation further confirms that the disciplinary orientations of the schools on the left and right are predominantly science-based and arts-based, respectively. Specifically, School 14 offers programs in literature, law, and art, whereas School 11 specializes in economics and management. Notably, in Figure 4, some teachers from School 14 are positioned closer to School 11— this is attributed to their expertise in commercial law, a field that shares close links with economic management. School 12 offers fundamental science disciplines, including mathematics, chemistry, and physics. Due to collaborative efforts between some of its teachers and those from other schools, an overlap between School 12 and other schools is observed in Figure 4. In addition, as shown in Figures 4–6, there are some subcategories comprising teachers from 6 schools. One possible explanation for this phenomenon is the existence of teachers with incomplete or missing information, whose representations appear in the central regions of Figures 4, 6. Another contributing factor is that some teachers are affiliated with school-wide courses (e.g., _Career Planning Courses for College Students_ located in the center of Figure 5) or school-wide research directions (e.g., _Research on University Teaching Exploration_ located in the right region of Figure 6). In summary, the vector representations of teachers generated by the AVRD model can effectively capture the underlying text information. 

## 5 Conclusion 

In this paper, we proposed an advisor recommendation model (AVRD), which employs BERT and unsupervised SimCSE to generate sentence vectors, to achieve recommendation based on students’ short-text queries. To enhance the recommendation performance, a pooling and matching criterion and a time-decay rate are integrated into the model. Empirical studies on the ground truth demonstrated the superiority of our proposed AVRD model. Notably, this model showed a significant improvement in accuracy, outperforming other LLMs such as Qwen and DeepSeek, as well as the traditional recommendation models like TF-IDF, LSA, and Word2Vec. Although the model training process is timeconsuming, often taking several hours, the inference process (i.e., providing recommendations for a student) is nearly instantaneous. Due to the challenges of collecting high-quality student datasets, we utilized a relatively small dataset. In future work, we will develop an advisor recommendation system based on our proposed model to support students in learning and innovative activities. As data accumulates, we will construct a larger dataset to further enhance recommendation accuracy. 

## Data availability statement 

The datasets presented in this study can be found in online repositories. The names of the repository/repositories and accession number(s) can be found below: https://github.com/ wxmwer/AVRD. 

## Ethics statement 

Ethical approval was not required for the study involving humans in accordance with the local legislation and institutional 

requirements. Written informed consent to participate in this study was not required from the participants or the participants’ legal guardians/next of kin in accordance with the national legislation and the institutional requirements. 

## Author contributions 

XW: Conceptualization, Formal analysis, Writing – review & editing, Methodology, Validation, Data curation, Writing – original draft, Software. JZ: Methodology, Formal analysis, Data curation, Software, Conceptualization, Writing – original draft, Visualization. LJ: Funding acquisition, Conceptualization, Supervision, Resources, Validation, Project administration, Writing – review & editing. YY: Writing – review & editing, Software, Data curation, Investigation, Formal analysis. LL: Writing – review & editing, Resources, Data curation, Validation. 

## Funding 

The author(s) declare that financial support was received for the research and/or publication of this article. This work was supported by the Laboratory Projects of “Institute for Digital Transformation” and “Institute for Energy System Intelligent Management and Policy Simulation” of China University of Petroleum, the Teaching Case Dataset Construction Project on Postgraduate Education of Shandong Province (Grant No. SDYAL2023030), the Teaching Research and Reform Project at China University of Petroleum (Grant No. CM2024050), and the Postgraduate Program Construction Project for _Big Data Analytics_ at China University of Petroleum (Grant No. UPCYZH-2025-15). 

###### 

The authors declare that the research was conducted in the absence of any commercial or financial relationships that could be construed as a potential conflict of interest. 

## Generative AI statement 

The author(s) declare that no Gen AI was used in the creation of this manuscript. 

Any alternative text (alt text) provided alongside figures in this article has been generated by Frontiers with the support of artificial intelligence and reasonable efforts have been made to ensure accuracy, including review by the authors wherever possible. If you identify any issues, please contact us. 

## Publisher’s note 

All claims expressed in this article are solely those of the authors and do not necessarily represent those of their affiliated organizations, or those of the publisher, the editors and the reviewers. Any product that may be evaluated in this article, or claim that may be made by its manufacturer, is not guaranteed or endorsed by the publisher. 

frontiersin.org 

Frontiers in Education 

10.3389/feduc.2025.1673956 

Wang et al. 

## References 

Alghamdi, S., Alzhrani, N., and Algethami, H. (2019). “Fuzzy-based recommendation system for university major selection,” in _Proceedings of the 11th International Joint Conference on Computational Intelligence_ (Setúbal: Science and Technology Publications), 317–324. doi: 10.5220/0008071803170324 

Brown, T. B., Mann, B., Ryder, N., Subbaih, M., Kalpan, J., Dhariwal, P., et al. (2020). “Language models are few-shot learners,” in _Advances in Neural Information Processing Systems 33_ , 1877–1901. doi: 10.48550/arXiv.2005.14165 

Christoforidou, M., and Kyriakides, L. (2021). Developing teacher assessment skills: the impact of the dynamic approach to teacher professional development. _Stud. Educ. Eval_ . 70:101051. doi: 10.1016/j.stueduc.2021.101051 

Cui, C., Zong, J., Ma, Y., Wang, X., Guo, L., Chen, M., et al. (2022). Tri-branch convolutional neural networks for Top-k focused academic performance prediction. _IEEE Trans. Neural Netw. Learn. Syst_ . 35, 1–12. doi: 10.1109/TNNLS.2022.3175068 

Cui, Y., Che, W., Liu, T., Qin, B., and Yang, Z. (2021). Pre-training with whole word masking for Chinese BERT. _IEEE/ACM Transactions on Audio, Speech, and Language Processing_ . 29, 3504–3514. doi: 10.1109/TASLP.2021.3124365 

DeepSeek-AI (2024a). DeepSeek-V2: a strong, economical, and efficient mixture-of-experts language model. _arXiv_ [preprint] arXiv:2405.04434. doi: 10.48550/arXiv.2405.04434 

DeepSeek-AI (2024b). _DeepSeek-LLM-7B-Chat [Computer software]_ . Hugging Face. Available online at: https://huggingface.co/deepseek-ai/deepseek-llm-7b-chat (Accessed September 22, 2025). 

Deerwester, S., Dumais, S. T., Furnas, G. W., Landauer, T., and Harshman, R. (1990). Indexing by latent semantic analysis. _J. Am. Soc. Inform. Sci_ . 41, 391–407. doi: 10.1002/(sici)1097-4571(199009)41:6<391::aid-asi1>3.0.co;2-9 

Deshpande, M., and Karypis, G. (2004). Item-based top-N recommendation algorithms. _ACM Trans. Inform. Syst_ . 22, 143–177. doi: 10.1145/963770.963776 

Devlin, J., Chang, M. W., Lee, K., and Toutanova, K. (2019). “BERT pretraining of deep bidirectional transformers for language understanding,” in _Proceedings of the 2019 Conference of the North American Chapter of the Association for Computational Linguistics_ (Medford: Association for Computational Linguistics), 4171–4186. doi: 10.18653/v1/N19-1423 

Esmeli, R., Bader-El-Den, M., and Abdullahi, H. (2020). “Using Word2Vec recommendation for improved purchase prediction,” in _Proceedings of the 2020 International Joint Conference on Neural Networks (IJCNN)_ (Glasgow, UK: IEEE), 1–8. doi: 10.1109/IJCNN48605.2020.9206871 

Evangelopoulos, N., Zhang, X., and Prybutok, V. R. (2012). Latent semantic analysis: five methodological recommendations. _Eur. J. Inform. Syst_ . 21, 70–86. doi: 10.1057/ejis.2010.61 

Fan, W., Ma, Y., Li, Q., He, Y., Zhao, E., Tang, J., et al. (2019). “Graph neural networks for social recommendation,” in _Proceedings of the WWW ’19: The World Wide Web Conference_ (New York, NY: ACM), 417–426. doi: 10.48550/arXiv.1902.07243 

Gao, T., Yao, X., and Chen, D. (2021). “SimCSE: simple contrastive learning of sentence embeddings,” in _Proceedings of the 2021 Conference on Empirical Methods in Natural Language Processing. Dominican Republic_ (Medford: Association for Computational Linguistics (ACL)), 6894–6910. doi: 10.18653/v1/2021.emnlp-main.552 

Goldberg, P., Wagner, W., Seidel, T., and Stürmer, K. (2023). Why do students exhibit different attention-related behavior during instruction? investigating effects of individual and context-dependent determinants. _Learn. Instruct_ . 83:101694. doi: 10.1016/j.learninstruc.2022.101694 

Gordon, V. N., and Steele, G. E. (2015). _The Undecided College Student: an Academic and Career Advising Challenge_ . Springfield, Illinois: Charles C. Thomas Publishers. 

Gu, J., Song, C., Jiang, W., Wang, X., and Liu, M. (2020). “Enhancing personalized trip recommendation with attractive routes,” in _Proceedings of the AAAI Conference on Artificial Intelligence_ (Palo Alto, CA: AAAI Press), 662–669. doi: 10.1016/j.knosys.2025.113639 

Gu, X., Zhao, L., and Jiang, L. (2022). Sequence neural network for recommendation with multi-feature fusion. _Expert Syst. Appl_ . 210, 118459. doi: 10.1016/j.eswa.2022.118459 

Guo, H., Tang, R., Ye, Y., Li, Z., and He, X. (2017). “DeepFM: a factorizationmachine based neural network for CTR prediction,” in _Proceedings of the 26th International Joint Conference on Artificial Intelligence_ (San Jose, CA: IJCAI Organization), 1725–1731. doi: 10.48550/arXiv.1703.04247 

Huang, L., Fu, M., Li, F., Qu, H., Liu, Y., and Chen, W. (2021). A deep reinforcement learning based long-term recommender system. _Knowl.-Based Syst_ . 213:106706. doi: 10.1016/j.knosys.2020.106706 

Imran, M., Latif, S., Mehmood, D., and Shah, M. S. (2019). Student academic performance prediction using supervised learning techniques. _Int. J. Emerg. Technol. Learn_ . 14, 92–104. doi: 10.3991/ijet.v14i14.10310 

Johnson, J., Douze, M., and Jégou, H. (2019). Billion-scale similarity search with GPUs. _IEEE Trans. Big Data_ 7, 535–547. doi: 10.1109/TBDATA.2019.2921572 

Jones, K. S. A. (1972). statistical interpretation of term specificity and its application in retrieval. _J. Document_ . 28, 11–21. doi: 10.1108/eb026526 

Karthik, R. V., and Ganapathy, S. A. (2021). fuzzy recommendation system for predicting the customers interests using sentiment analysis and ontology in e- commerce. _Appl. Soft Comp_ . 108:107396. doi: 10.1016/j.asoc.2021.107396 Kiri¸sci, M. (2023). New cosine similarity and distance measures for Fermatean fuzzy sets and TOPSIS approach. _Knowl Inf Syst_ . 65, 855–868. doi: 10.1007/s10115-022-01776-4 Lee, K. (2017). Rethinking the accessibility of online higher education: a historical review. _Intern. Higher Educ_ . 33, 15–23. doi: 10.1016/j.iheduc.2017.01.001 

Li, Y., and Shen, B. (2017). “Research on sentiment analysis of microblogging based on LSA and TF-IDF,” in _Proceedings of the 3rd IEEE International Conference on Computer and Communications (ICCC)_ (Danvers: IEEE), 2584–2588. doi: 10.1109/CompComm.2017.8323002 

Liu, Y., Yang, S., Xu, Y., Miao, C., Wu, M., Zhang, J. (2023). Contextualized graph attention network for recommendation with item knowledge graph. _IEEE Trans. Knowl. Data Eng_ . 35, 181–195. doi: 10.1109/TKDE.2021.3082948 

Luo, C., Zhan, J., Xue, X., Wang, L., Ren, R., and Yang, Q. (2018). “Cosine normalization: Using cosine similarity instead of dot product in neural networks,” in _Proceedings of the International Conference on Artificial Neural Networks-ICANN_ (Cham: Springer), 382–391. doi: 10.48550/arXiv.1702.05870 

Ma, H., Wang, X., Hou, J., and Lu, Y. (2017). “Course recommendation based on semantic similarity analysis,” in _Proceedings of the 2017 3rd IEEE International Conference on Control Science and Systems Engineering (ICCSSE)_ (New York, NY: IEEE), 638–641. doi: 10.1109/CCSSE.2017.8088011 

Maaten, L. v. d., and Hinton, G. E. (2008). Visualizing data using t-SNE. _J. Mach. Learn. Res_ . 9, 2579–2605. Available online at: http://jmlr.org/papers/v9/ vandermaaten08a.html 

Manning, C. D., Raghavan, P., and Schütze, H. (2008). _Introduction to Information Retrieval_ . New York, USA: Cambridge university press. 

Mikolov, T., Chen, K., Corrado, G., and Dean, J. (2013a). “Efficient estimation of word representations in vector space,” in _Proceedings of the 1st International Conference on Learning Representations_ (Brookline: Journal of Machine Learning Research). doi: 10.48550/arXiv.1301.3781 

Mikolov, T., Sutskever, I., Chen, K., Corrado, G., and Dean, J. (2013b). “Distributed representations of words and phrases and their compositionality,” in _Proceedings of the NIPS’13: Neural Information Processing Systems_ (New Yok, NY: Curran Associates), 3111–3119. doi: 10.48550/arXiv.1310.4546 

Morin, F., and Bengio, Y. (2005). “Hierarchical probabilistic neural network language model,” in _Proceedings of the 10th International Workshop on Artificial Intelligence and Statistics_ (Brookline: Society for Artificial Intelligence and Statistics), 246–252. Available online at: https://proceedings.mlr.press/r5/morin05a/morin05a.pdf 

Nilashi, M., Minaei-Bidgoli, B., Alghamdi, A., Alrizq, M., Alghamdi, O., Nayer, F. K., et al. (2022). Knowledge discovery for course choice decision in Massive Open Online Courses using machine learning approaches. _Expert Syst. Appl_ . 199:117092. doi: 10.1016/j.eswa.2022.117092 

Obeid, C., Lahoud, C., El Khoury, K., and Champin, P. A. (2022). A novel hybrid recommender system approach for student academic advising named COHRS, supported by case-based reasoning and ontology. _Comp. Sci. Inform. Syst_ . 19, 979–1005. doi: 10.2298/CSIS220215011O 

Ouyang, L., Wu, J., Jiang, X., Almeida, D., Wainwirght, C., Mishkin, P., et al. (2022). Training language models to follow instructions with human feedback. _arXiv_ [preprint] arXiv:220302155. doi: 10.48550/arXiv.2203.02155 

Oztas, G. S., and Akcapinar, G. (2025). Predicting students’ academic procrastination tendencies using online learning trajectories. _Educ. Technol. Soc_ . 28, 77–93. doi: 10.30191/ETS.202504_28(2).RP05 

Pargett, K. K. (2011). _The Effects of Academic Advising on College Student Development in Higher Education_ (Educational Administration: Theses, Dissertations, and Student Research), 81. Available online at: https://digitalcommons.unl.edu/ cehsedaddiss/81 

Qwen team (2024). _Qwen2.5-7B [Computer software]_ . Hugging Face. Available online at: https://huggingface.co/Qwen/Qwen2.5-7B (Accessed September 22, 2025). 

Ricci, F., Rokach, L., and Shapira, B. (2022). _Recommender Systems Handbook_ . New York, NY: Springer. 

Thierry, N., Bao, B. K., Chatelain, I. B. C., Tan, Z., Ali, Z., and Kefalas, P. (2023). PRM-KGED: paper recommender model using knowledge graph embedding and deep neural network. _Appl. Intellig_ . 53, 30535–30551. doi: 10.1007/s10489-023-05162-7 

frontiersin.org 

Frontiers in Education 

10.3389/feduc.2025.1673956 

Wang et al. 

Thongtan, T., and Phienthrakul, T. (2019). “Sentiment classification using document embeddings trained with cosine similarity,” in _Proceedings of the 57th Annual Meeting of the Association for Computational Linguistics: Student Research Workshop_ (Medford: Association for Computational Linguistics (ACL)), 407–414. doi: 10.18653/v1/P19-2057 

Wang, J., Au, O. T. S., Wang, F. L., Xie, H., and Zou, D. (2020). “Attention-based CNN for personalized course recommendations for MOOC learners,” in _Proceedings of the 2020 International Symposium on Educational Technology (ISET)_ (New York, NY: IEEE), 180–184. doi: 10.1109/ISET49818.2020.00047 

Wang, X., Ma, W., Guo, L., Jiang, H., Liu, F., and Xu, C. (2022). HGNN: hyperedge-based graph neural network for MOOC course recommendation. _Inform. Proc. Managem_ . 59:102938. doi: 10.1016/j.ipm.2022.102938 

Wang, Z., Wei, W., Xu, C., Xu, J., and Mao, X. (2022). Person-job fit estimation from candidate profile and related recruitment history with co-attentionf neural networks. _Neurocomputing_ . 501, 14–24. doi: 10.1016/j.neucom.2022.06.012 

Wankat, P. C. (2005). Undergraduate student competitions. _J. Eng. Educ_ . 94, 343–347. doi: 10.1002/j.2168-9830.2005.tb00860.x 

Wu, J., and Feng, Q. (2020). Recommendation system design for college network education based on deep learning and fuzzy uncertainty. _J. Intellig. Fuzzy Syst_ . 38, 7083–7094. doi: 10.3233/JIFS-179787 

Wu, L., He, X., Wang, X., Zhang, K., and Wang, M. (2023). A survey on accuracy-oriented neural recommendation: from collaborative filtering to information-rich recommendation. _IEEE Trans. Knowl. Data Eng_ . 35, 4425–4445. doi: 10.1109/TKDE.2022.3145690 

Yang, A., Yang, B., Hui, B., Zheng, B., Yu, B., Zhou, C., et al. (2024). Qwen2 technical report. _arXiv_ [preprint] arXiv:240710671. doi: 10.48550/arXiv.2407.10671 

Yang, Z., Yang, J., Du, X., Rice, K., and Hung, J. L. (2020). Using convolutional neural network to recognize learning images for early warning of at-risk students. _IEEE Trans. Learn. Technol_ . 13, 617–630. doi: 10.1109/TLT.2020.2988253 

Yu, Q. (2022). Factors influencing online learning satisfaction. _Front. Psychol_ . 13, 852360–852360. doi: 10.3389/fpsyg.2022.852360 

Zemaityte, G., and Terzi´c, K. (2019). “Supervisor recommendation tool for computer science projects,” in _CEP’19: Proceedings of the 3rd Conference on Computing Education Practice_ (New York, NY: ACM), 1–4. doi: 10.1145/3294016.3294030 

Zhang, C., Xue, S., Li, J., Wu, J., Du, B., Liu, D., et al. (2023). Multi-aspect enhanced graph neural networks for recommendation. _Neural Netw_ . 157, 90–102. doi: 10.1016/j.neunet.2022.10.001 

Zhang, J., Hao, B., Chen, B., Li, C., Chen, H., and Sun, J. (2019). “Hierarchical reinforcement learning for course recommendation in MOOCs,” in _Proceedings of the AAAI Conference on Artificial Intelligence_ (Palo Alto, CA: AAAI Press), 435–442. doi: 10.1609/aaai.v33i01.3301435 

Zhang, M., Liu, Z., Sun, J., Ma, J., and Silva, T. (2016). A research analytics framework-supported recommendation approach for supervisor selection. _Br. J. Educ. Technol_ . 47, 403–420. doi: 10.1111/bjet. 12244 

Zou, W., Zhong, W., Du, J., and Yuan, L. (2025). Prediction of student academic performance utilizing a multi-model fusion approach in the realm of machine learning. _Appl. Sci-Basel_ . 17:3550. doi: 10.3390/app15073550 

frontiersin.org 

Frontiers in Education
