# **A BERT Based Hybrid Recommendation System For Academic Collaboration**<sup>**_⋆_**</sup> 

Sangeetha N<sup>1</sup> , Harish Thangaraj<sup>2</sup> , Varun Vashisht<sup>3</sup> , Eshaan Joshi<sup>4</sup> , Kanishka Verma<sup>5</sup> , and Diya Katariya<sup>6</sup> 

Vellore Institute of Technology, Chennai, India `n.sangeetha@vit.ac.in`<sup>1</sup> `harish.thangaraj03@outlook.com`<sup>2</sup> `varunvashisht.work@gmail.com`<sup>3</sup> `eshaanjoshi.work@gmail.com`<sup>4</sup> `kanishkaverma2003@gmail.com`<sup>5</sup> `diyakatariya0907@gmail.com`<sup>6</sup> 

**Abstract.** Universities serve as a hub for academic collaboration, promoting the exchange of diverse ideas and perspectives among students and faculty through interdisciplinary dialogue. However, as universities expand in size, conventional networking approaches via student chapters, class groups, and faculty committees become cumbersome. To address this challenge, an academia-specific profile recommendation system is proposed to connect like-minded stakeholders within any university community. This study evaluates three techniques: Term Frequency-Inverse Document Frequency (TF-IDF), Bidirectional Encoder Representations from Transformers (BERT), and a hybrid approach to generate effective recommendations. Due to the unlabelled nature of the dataset, Affinity Propagation cluster-based relabelling is performed to understand the grouping of similar profiles. The hybrid model demonstrated superior performance, evidenced by its similarity score, Silhouette score, DaviesBouldin index, and Normalized Discounted Cumulative Gain (NDCG), achieving an optimal balance between diversity and relevance in recommendations. Furthermore, the optimal model has been implemented as a mobile application, which dynamically suggests relevant profiles based on users’ skills and collaboration interests, incorporating contextual understanding. The potential impact of this application is significant, as it promises to enhance networking opportunities within large academic institutions through the deployment of intelligent recommendation systems. 

**Keywords:** academic networking · university collaboration · hybrid recommendation system · TF-IDF · cosine similarity · BERT · affinity propagation · NDCG 

> _⋆_ Vellore Institute of Technology, Chennai, India 

2 Sangeetha N et al. 

## **1 Introduction** 

Networking is a fundamental skill crucial for both professional advancement and personal development. In the dynamic landscape of modern university environments, fostering intellectual exploration and academic collaboration is paramount. However, as institutions grow in size and complexity, the task of meaningful interaction becomes increasingly challenging. Consider a newly enrolled student navigating a large university campus, eagerly seeking opportunities to collaborate on projects and connect with like-minded individuals. The traditional methods of networking such as approaching student groups and seeking guidance from professors though time-honored, often prove to be cumbersome and inefficient. These methods demand significant time and energy, leading to delays in the execution of several ideas. 

This article aims to address the inherent limitations and inefficiencies of traditional networking methods within a university setting by proposing a digital networking solution operating in academic circles. Enter ‘FindMate’ – a swipebased recommendation system presented in the form of a mobile application. While TF-IDF has been a popular approach to build such systems due to its simplicity and effectiveness in text representation, BERT [6] with its deep contextual embeddings and superior understanding of language nuances offers better capturing of semantic meaning and thereby better recommendations. This paper proposes a system leveraging the best of both techniques for our use case, a hybrid model that uses both TF-IDF and BERT embeddings. The study assesses all three techniques on custom generated profile data. The results provide a clear evaluation on each of the model’s capability to group variety of cross-domain data. The recommendation approach with the best metrics is integrated into the mobile app revolutionizing the concept of campus networking with contextual awareness and language understanding. 

Despite the widespread adoption of recommendation systems in academic circles, the existing literature predominantly focuses on facilitating access to research papers, certain academic resources and there is limited work on integrating language models like BERT for the same. However, this study endeavours to extend the scope of academic recommendation systems to include the realm of profile connections within university environments using the mentioned hybrid approach. ‘FindMate’ aims to bridge the gap between individuals with similar interests and aspirations, thereby fostering collaborative partnerships and catalyzing innovative projects. 

## **2 Related Works** 

### **2.1 Social Media for Networking** 

The web is full of networking solutions to facilitate connections for academia. Several existing platforms and research studies have explored similar objectives, aiming to address the challenges associated with traditional networking. From approaching peers in person to connecting on digital platforms, there have been 

Hybrid Recommendation System for Academia 

significant advancements in networking methodologies. Udenze and Silas [16] in their paper study academic awareness and the usage of LinkedIn for academic networking using a non-probability-based sampling method and conclude that scholars have not fully embraced the power of LinkedIn to build academic collaboration due to the low level of awareness. Similarly, Ritesh Chugh et al [4] conducted a scoping survey review focusing on the use of social media by academics highlighting the need for higher education institutions to provide awareness to its stakeholders of the applicability of social media for academia. A study on the benefits and problems of using general social networking sites in the scholastic domain has concluded to be a lot more negative than positive as observed by Katy Jordan and Martin Weller [9] in their research. Generic social networking platforms prioritize commercial usage and marketing rather than upholding academic interests. On a more fundamental side, Heffernan [8] studies consequences of academic networking and surveys the effect on overall career opportunities and faculty relationships. His findings suggest that merit-based achievements can be overshadowed by network opportunities and good connections impact individual’s aspirations proving to be a key motivation to our work. 

### **2.2 Academic Specific Networking and Recommendation Systems** 

Conole et al [5] present ’Cloudworks’ a web application developed to share learning and teaching ideas across users. The work also explores various theoretical frameworks towards connecting people and the downside of maintaining a self sustaining user base for such platforms. Adeniyi et al [1] in their paper, investigate the significance of academic-specific social networking tools in contrast to the generic platforms discussed previously. The study highlights the poor usability of such niche-specific platforms and proves to be clumsy for a new user, spending a significant amount of time getting acquainted with the interface. Further, academic sites lack a personalized touch of giving you relevant collaboration information due to the sheer volume of data as concluded by Kong et al [12] in their publication. Ko et al [11] review recommendation models and techniques giving comprehensive overview of existing systems in application service fields further enhancing the basic understanding of such systems for this study . Zhang et al [18] investigate various scholarly recommendation systems including literature and author collaborator systems emphasising the importance for better techniques than content based and collaborative filtering. Nikhat Akhtar and Devendra Agarwal [2] explore a research paper recommendation system employing several machine learning paradigms . Similarly, a program and course recommendation algorithm was discussed by Mohammed Ezz and Ayman Elshenawy [7] in their research . StudieMe is a college suggestion system published by Vidish Sharma et al [14] that uses text-based Cosine Similarity to match relevant data. Presenting a collaborative filtering recommendation system, Jianjun Ni et al [13] use a two step approach applying TF-IDF and fuzzy logic to recommend data. 

4 Sangeetha N et al. 

### **2.3 Deep Learning Based Recommendation Systems** 

The previous references primarily focused on conventional methods for building recommendation systems. Shifting our literature survery towards deep learning architectures, Rodríguez-Hernández et al [3] conducted an experimental study where they compare traditional recommendation algorithms with BERT based ones and conclude that while BERT techniques have been used well in Natural Language Processing tasks the exploitation of BERT in recommendation systems is a sparsely unexplored topic. Juarto and Girsang [10] in their work present a neural system integrating Sentence BERT with a collaborative news recommender. This integration results in a significant increase in hit ratio indicating the positive impact using deep architectures. A text recommendation system fused with Convoluted neural networks (CNN) and BERT semantic information was offered by Xingyun Xie et al [17] demonstrating an improved feature extraction mechanism when performing small scale Top-N recommendations. 

## **3 The Proposed Solution and Implementation** 

The development of the solution consists of 5 broad segments. 

### **3.1 Initial Data Extraction and Pre-Processing** 

The foundational text data was collected through a survey designed to gather user information, including experience, collaboration interests, and skills, as well as credentials such as name and email. To augment the dataset, we generated additional synthetic data for the same fields by defining a manual pool of skill set and domain interest, thereby creating a large and diverse corpus for comprehensive model training and testing (refer Fig.1 for proportion). Following data collection, the dataset was thoroughly pre-processed using the Natural Language Toolkit (NLTK) and loaded onto MongoDB for real time storage and retrieval. 

Table 1: Structural view of the survey dataset 

|Column|Data type|Description|
|---|---|---|
|Name|String|Full name of the user|
|Email|String|Email of the user|
|Profession|String|Profession of the user|
|Experience|number|Experience in years|
|Interest|String|Type of academic activity|
|Collaboration with|String|Preferred collaborators|
|Domain|String|Domain of collaboration|
|Skillset|String|Skillset of the user|

6 Sangeetha N et al. 

### **3.2 TF-IDF** 

TF-IDF and Cosine Similarity are established methodologies to build recommendation algorithms. TF represents the frequency of a term relative to the total word count. IDF reflects the proportion of documents in the corpus that contain the term. TF-IDF together specify the relevancy of a specific term in the given corpus in the form of a numerical vector. Columns ‘domain’ and ‘skillset’ are combined and vectorized using the mentioned TF-IDF technique to grab relevant information on the user’s skills and interests. 

Subsequently, Cosine Similarity is applied to these vectors to quantify the similarity between them. It calculates the cosine of the angle between the two vectors in a multidimensional space, signifying their orientations. Documents pointing in similar directions will have similar content, yielding a higher cosine similarity score which acts as the fundamental criteria to filter out relevant user profiles. 

The pre-processed data points were vectorized to compute the cosine similarity matrix, (workflow given by Algorithm.1). Given the unsupervised variable nature of the text, similar profiles were grouped together using Affinity Propagation, a clustering method that automatically determines the optimal number of clusters operating on the calculated cosine similarity matrix. The resulting high dimension groups were reduced using t-distributed Stochastic Neighbor Embeddings (t-SNE) and plotted for visualization. 

All profiles in the dataset were labeled (cluster based relabeling) with their respective cluster numbers for further evaluation and bench-marking of cohesion which is discussed under results. 

### **3.3 BERT** 

BERT’s architecture comprises transformer encoders that process input text bidirectionally, capturing contextual relationships between words. As in the case of profile matching, nuanced meanings and relationships between the sequences are grabbed effectively. The input data is tokenized using a Masked and Permuted Pre-training Network (MPNet) Tokenizer [15]. The pre-trained ’bertlarge-uncased’ architecture [6] is fine tuned on the input sequence to facilitate 

10 Sangeetha N et al. 

target profiles, mAP provides a comprehensive measure of the system’s ability to recommend relevant profiles, ensuring that the most similar and relevant profiles are prioritized. 

Table 2: Metrics 

|Metric|BERT|TF-IDF|Hybrid|
|---|---|---|---|
|Davies-Bouldin|1.5238|1.0342|1.1052|
|Sillhouette|0.1578|0.3876|0.3383|
|Intra-Cluster|0.8819|0.7430|0.8053|
|NDCG|0.8329|0.7634|**0.8587**|
|MAP|0.7858|0.8112|**0.8275**|

BERT provides the highest intra-cluster similarity, indicating that profiles within each cluster are highly relevant to each other, thanks to its superior contextual understanding. However, BERT’s lower Silhouette and Davies-Bouldin scores suggest less distinct clustering, which may lead to profiles appearing in multiple clusters. This overlap can be advantageous for recommending diverse profiles. The NDCG score close of 0.8329 being closer to 1, indicates that BERT has strong ranking quality, meaning it successfully prioritizes profiles that closely match the intended user needs. A mAP of 0.7858 suggests that, on average, BERT achieves a high degree of precision across recommendations. 

TF-IDF shows a high Silhouette score, reflecting clear distinctions between clusters based on keyword frequency. While this leads to distinct grouping, it can be limiting when user data is expected to be inclusive among more than one clusters, potentially resulting in less accurate recommendations. Additionally, TF-IDF’s lack of contextual understanding may hinder performance by missing nuanced features and ignoring text that means the same but has been expressed differently by various users. The relatively lower NDCG score than BERT aligns with the lack of contextual understanding. The mAP score proves to be higher than that of BERT reflecting ability to achieve precise clustering. 

The results of the hybrid system indicate it is the most optimal method, striking a balance between clear cluster distinctions and high contextual understanding. This approach provides both effective separation of profiles into varied clusters and high relevance in recommendations, ensuring accuracy even with diverse data. The high NDCG score indicates that the hybrid approach successfully leverages BERT’s semantic understanding while maintaining the clear 

Hybrid Recommendation System for Academia 

cluster separations created by TF-IDF. Further, it also achieves greater precision as suggested by mAP metrics. 

Further each of these systems were tested to recommend top 5 relevant profiles. The first step involved pulling out a target user as per Table 3 for whom profile recommendations would be generated. Then the recommendation functions were defined and called where the target user’s data would be compared with the embeddings or vectors of the systems to return the top 5 profiles as shown in Table. 

Table 3: Target user data 

Fields Value Name Jeffery Hammond Email jeffery.hammond@example.com Domain Cybersecurity Skillset C, C++, Python, Java, HTML, CSS, SQL Cluster 101, 97, 102 (TF-IDF,BERT,Hybrid) 

The variations in similarity scores and cluster assignments across the three methods highlight differences in how effectively each model captures information. Specifically, the increase in scores from TF-IDF to the Hybrid approach indicates that incorporating BERT’s contextual understanding enhances performance, providing a more accurate representation of the profiles. Overall, the algorithms demonstrated promising outcomes with their target specific user suggestions as per Table.4. 

The process was iterated for various profile data combinations Each iteration produced satisfactory results, with relevant profiles consistently being recommended. 

## **5 Shortcomings** 

Despite the favourable algorithm results, there are a few shortcomings we observed during our study which we would like to highlight: 

All three methods rely heavily on abundant availability of data to begin with, a challenge commonly known as the ‘cold start problem’. Without historical data, accurate profile hits were not generated. Our survey dataset served as the foundational data required to implement the algorithm, followed by the creation of synthetic profiles that were added to the existing corpus. When applied to incorrect and noisy data, TF-IDF establishes inaccurate relationships, as it cannot identify semantic nuances explicitly. The performance of small data is also hindered as it may fail to assess the relevance of terms, potentially resulting in unreliable recommendations. BERT is highly resource-intensive, often requiring significant computational power and memory, which leads to longer tokenization 

12 Sangeetha N et al. 

Table 4: Recommendations 

(a) TF-IDF Recommendations 

|**Name**|**Domain/Skillset**|**Similarity sco**|**re**<br>**Cluster**|
|---|---|---|---|
|Joan Evans|ai ml, C, C++, Python,<br>Java, HTML, CSS|0.9803|101|
|Gregory Williamson|Data<br>Mining,<br>C,<br>C++,<br>Python, Java, SQL|0.9714|101|
|Alexis Moore|Java,<br>HTML,<br>CSS,<br>SQL,<br>AWS, FlutterFlow|0.9524|101|
|Joshua Hughes|Marketing,Game design, C,<br>C++, Pytho|0.9439|101|
|Jacob Harper|SQL, AWS, Figma, Canva,<br>Adobe XD|0.8797|101|

#### (b) BERT Recommendations 

|**Name**|**Domain/Skillset**|**Similarity sco**|**re**<br>**Cluster**|
|---|---|---|---|
|Gregory Williamson|Data<br>Mining,<br>C,<br>C++,<br>Python, Java, SQL|0.9895|97|
|Alexis Moore|Java,<br>HTML,<br>CSS,<br>SQL,<br>AWS, FlutterFlow|0.9894|97|
|Joan Evans|ai ml, C, C++, Python,<br>Java, HTML, CSS|0.9863|97|
|Joshua Hughes|Marketing,Game design, C,<br>C++, Python|0.9846|97|
|Paula Russell|Cyber<br>security,<br>python,<br>HTML, ReactJS|0.9221|83|

#### (c) Hybrid Recommendations 

|**Name**|**Domain/Skillset**|**Similarity sco**|**re**<br>**Cluster**|
|---|---|---|---|
|Joan Evans|ai ml, C, C++, Python,<br>Java, HTML, CSS|0.9833|102|
|Gregory Williamson|Data<br>Mining,<br>C,<br>C++,<br>Python, Java, SQL|0.9804|102|
|Alexis Moore|Java,<br>HTML,<br>CSS,<br>SQL,<br>AWS, FlutterFlow|0.9709|102|
|Joshua Hughes|Marketing,Game design, C,<br>C++, Pytho|0.9643|102|
|Paula Russell|Cyber<br>security,<br>python,<br>HTML, ReactJS|0.8943|102|

Hybrid Recommendation System for Academia 

and processing times. BERT is also highly data sensitive and improper tuning can possibly affect the model’s biases. As data size grows, unchecked integration of data into the hybrid model can lead to an influx of noisy information. This can result in reduced accuracy, overfitting, and diminished model interpretability. 

## **6 Conclusion and Future work** 

This study concluded by suggesting a unique use case and a hybrid system for profile recommendation systems that offers a promising solution to the limitations encountered with traditional methods of networking. Three recommendation models were also systematically evaluated and the hybrid approach was optimal by capturing both frequencies and contextual data, facilitating more efficient networking within academic circles. Testing on multiple target user combinations, it was demonstrated that the system was able to yield relevant recommendations. An end-to-end development setup including a mobile application was also highlighted. Looking ahead, the focus is on advancing the algorithm by incorporating more sophisticated and nuanced techniques to further enhance accuracy and effectiveness such as collaborative recommendation techniques. Furthermore, application will be taken to production after due optimization and be implemented within universities and various student communities. 

**Acknowledgements** We thank our mentor Dr. Sangeetha N whose guidance and feedback have been instrumental in shaping this research work and manuscript. This research was supported by our institution Vellore Institute of Technology, Chennai. We thank our academic department and colleagues for their insight and expertise that greatly assisted the research. 

## **References** 

1. ADENIYI, A., TIAMIYU, M., MBADA, K.A., ADENIYI, W., OLAKOREDE, D., ADEMOYEGUN, A.B., MBADA, C.E.: Academic social networking tools usage among nigerian university academics. Mediterranean Journal of Education **4** , 39–54 (1 2024). https://doi.org/10.26220/MJE.4638, https://mgdlt.lis.upatras.gr/ mje/article/view/4638 

2. Akhtar*, N., Agarwal, P.D.D.: An efficient mining for recommendation system for academics. International journal of recent technology and engineering **8** , 1619–1626 (1 2020). https://doi.org/10.35940/IJRTE.E5924.018520 

3. Carmenrodriguez-Hernandez, M.D., Del-Hoyo-Alonso, R., Ilarri, S., MontanesSalas, R.M., Sabroso-Lasa, S.: An experimental evaluation of content-based recommendation systems: Can linked data and bert help? Proceedings of IEEE/ACS International Conference on Computer Systems and Applications, AICCSA **2020November** (11 2020). https://doi.org/10.1109/AICCSA50499.2020.9316466 

4. Chugh, R., Grose, R., Macht, S.A.: Social media usage by higher education academics: A scoping review of the literature. Education and Information Technologies **26** , 983–999 (1 2021). https://doi.org/10.1007/S10639-020-10288-Z/METRICS, https://link.springer.com/article/10.1007/s10639-020-10288-z 

14 Sangeetha N et al. 

5. Conole, G., Galley, R., Culver, J.: Frameworks for understanding the nature of interactions, networking, and community in a social networking site for academic practice. The International Review of Research in Open and Distributed Learning **12** , 119–138 (3 2011). https://doi.org/10.19173/IRRODL.V12I3.914 

6. Devlin, J., Chang, M.W., Lee, K., Toutanova, K.: Bert: Pre-training of deep bidirectional transformers for language understanding (2019), https://arxiv.org/abs/ 1810.04805 

7. Ezz, M., Elshenawy, A.: Adaptive recommendation system using machine learning algorithms for predicting student’s best academic program. Education and Information Technologies **25** , 2733–2746 (7 2020). https://doi.org/10. 1007/S10639-019-10049-7/METRICS, https://link.springer.com/article/10.1007/ s10639-019-10049-7 

8. Heffernan, T.: Academic networks and career trajectory: ‘there’s no career in academia without networks’. Higher Education Research & Development **40** , 981– 994 (2021). https://doi.org/10.1080/07294360.2020.1799948 

9. Jordan, K., Weller, M.: Academics and social networking sites: Benefits, problems and tensions in professional engagement with online networking. Journal of Interactive Media in Education **2018** (2018). https://doi.org/10.5334/jime.448 

10. Juarto, B., Girsang, A.S.: Neural collaborative with sentence bert for news recommender system. JOIV : International Journal on Informatics Visualization **5** , 448–455 (12 2021). https://doi.org/10.30630/JOIV.5.4.678, http://joiv.org/index. php/joiv/article/view/678 

11. Ko, H., Lee, S., Park, Y., Choi, A.: A survey of recommendation systems: Recommendation models, techniques, and application fields. Electronics **11** (1) (2022). https://doi.org/10.3390/electronics11010141, https://www.mdpi.com/2079-9292/ 11/1/141 

12. Kong, X., Shi, Y., Yu, S., Liu, J., Xia, F.: Academic social networks: Modeling, analysis, mining and applications. Journal of Network and Computer Applications **132** , 86–103 (4 2019). https://doi.org/10.1016/J.JNCA.2019.01.029 

13. Ni, J., Cai, Y., Tang, G., Xie, Y.: Collaborative filtering recommendation algorithm based on tf-idf and user characteristics. Applied Sciences **11** (20) (2021). https: //doi.org/10.3390/app11209554, https://www.mdpi.com/2076-3417/11/20/9554 

14. Sharma, V., Trehan, T., Chanana, R., Dawn, S.: Studieme: College recommendation system. 2019 3rd International Conference on Recent Developments in Control, Automation and Power Engineering, RDCAPE 2019 pp. 227–232 (10 2019). https://doi.org/10.1109/RDCAPE47089.2019.8979030 

15. Song, K., Tan, X., Qin, T., Lu, J., Liu, T.Y.: Mpnet: Masked and permuted pretraining for language understanding (2020), https://arxiv.org/abs/2004.09297 

16. Udenze, S.: Investigatingacademics’ awareness & use of linkedin for professional networking. International Journal of Advanced Research and Publications www. ijarp.org 

17. Xie, X., Ren, Z., Gu, Y., Zhang, C.: Text recommendation algorithm fused with bert semantic information. ACM International Conference Proceeding Series pp. 223–227 (12 2021). https://doi.org/10.1145/3507548.3507582, https://dl.acm.org/ doi/10.1145/3507548.3507582 

18. Zhang, Z., Patra, B.G., Yaseen, A., Zhu, J., Sabharwal, R., Roberts, K., Cao, T., Wu, H.: Scholarly recommendation systems: a literature survey. Knowledge and Information Systems **65** , 4433–4478 (11 2023). https://doi.org/ 10.1007/S10115-023-01901-X/TABLES/18, https://link.springer.com/article/10. 1007/s10115-023-01901-x
