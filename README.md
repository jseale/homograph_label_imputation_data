# Homograph label imputation data
This repository contains data from the [Wikipedia Homograph Data](https://github.com/google/WikipediaHomographData) (WHD; Gorman et al., 2018), the Switchboard corpus (SWBD; Godfrey et al., 1992), and the French English Europarl corpus (Koehn, 2005). 

The datasets in this repo were generated during the dissertation work of Jen Seale, in which two kinds of homograph pronunication label imputation were developed and tested using machine learning homograph disambiguation models. Jen Seale's dissertation, Label imputation for homograph disambiguation: Theoretical and practical approaches, is to be deposited in September 2021. 

There are three folders of data in this repo: 
1) `Dissertation_Experiment_Data` contains the homograph disambiguation data used in dissertation experimentation. There are four different `train` splits, one `dev` and one `test` split. All these splits contain data for 68 pronunciation classes over 34 WHD homographs.
3) `SWBD_Data` contains the full set of hand-labeled Switchboard data generated over the course of the dissertation. 
4) `Europarl_Data` contains the full sets of hand-labeled and semi-automatedly labeled French English Europarl data generated over the course of the dissertation.  

This README is structured using the template provided in [Datasheets for Datasets](https://www.microsoft.com/en-us/research/uploads/prod/2019/01/1803.09010.pdf) as recommended for use by the Association for Computational Lingustics (ACL).

## Motivation
1. _Why was this dataset created?_

    This dataset was created for the purpose of homograph disambiguation modeling done to evaluate the effectiveness of two types of label imputation for homograph disambiguation: 1) label imputation from transcribed audio of conversational speech, 2) label imputation from parallel corpora. In the experiments, machine learning models are trained using the WHD train and dev splits alone, and then trained with the various train splits augmented using either label-imputed or hand-labeled data (and the non-augmented dev split) for comparison. The WHD-only test split is held out in order to report final metrics.

2. _Has this dataset been used for any tasks already? If so, what (other) tasks could this dataset be used for?_
 
    For the ALBERT, BERT, and XLNet models trained in the dissertation, the task is framed as token classification, or NER. The task could also be framed as contextualized homograph pronunciation classification in which the homograph, vectorized with its surrounding text is classified.

3. _Who funded the creation of the dataset?_

     This dataset was created during the dissertation of CUNY Graduate Center doctoral candidate, Jen Seale, whose studies at the university were primarily funded through a Presidential Research Fellowship. 

## Dataset composition
1. _What are the instances? What data does each instance consist of?_

    Each instance of the data used in the dissertation experiments contains three data points: 
    
    1) the text string for the homograph, found in the `homograph` column of each `tsv`

    2) a computationally friendly text string indicating a the pronunciation of the homograph in sentential context, found in the `wordid` column of each `tsv` (adopted from Gorman et al.'s 2018 Wikipedia Homograph Data)

    3) a text string sentence containing the homograph, found in the `sentence` column of each `tsv`.
    
    Additional data generated during the dissertation, but not used in its entirety during experimentation, is also provided in this repo. Hand-labeled data from the SWBD corpus is available in `swbd_hand_all`, and adds a `file_name` column to the three classes of data listed above, with which you can locate the data in the NXT Switchboard corpus. 

2. _How many instances of each type are there?_

    The `train`, `dev`, and `test` data splits contain 3,407 WHD samples. The `train` split contains 2719 samples, with about 80 samples for each of 34 WHD homographs in the data. The `dev` split contains 348 samples, with about 10 samples for each of the 34 WHD homographs in the data. The `test` split has contains 340 samples, with 10 samples for each of the 34 WHD homographs in the data. Stratified sampling is used as the default to maintain class distribution among the splits. This sampling technique is coupled with logic to enforce the presence of at least one sample per class in each split. Forcing class presence proved necessary due to the more extreme class imbalances present in the data, such as those in which only 3 instances of one of the two homograph pronunciations are available.

    Three additional train splits used in dissertation experimentation exist: `train_euro`, `train_swbd`, and `train_swbd_hand`, each of which add data to the same 2719 WHD train samples in the train split. 

    **`train_euro` data**
    The train_euro split augments the WHD with 718 samples from the French English Europarl corpus for a total of 3437 train samples. The Europarl samples are labeled using the Alignment-to-Pronunciation (AP) label imputation technique developed in the dissertation, which depends on the presence of disjoint sets of interlingual text word forms that align to homograph pronunciations.

    **`train_swbd` data**
    The train_swbd split augments the WHD with 147 samples from the Switchboard corpus for a total of 2866 train samples. The SWBD samples are labeled using label imputation from transcribed audio, developed in the dissertation as the first recorded attempt of its kind.

    **`train_swbd_hand` data**
    The train_swbd_hand split augments the WHD with 322 samples from the Switchboard corpus for a total of 3041 train samples. The SWBD samples are labeled by hand during the process of label imputation from transcribed audio. The data from the imputation process contains fewer samples due to overlap in imputed labels between homograph pronunciations. For example, the imputed pronunciations for _excuse_, as in "an excuse to do", which should all be mappable to /ək'skju:s/, also contain pronunciations that map to this homograph's alternate pronunciation, /ək'skju:z/.

3. _Is everything included or does the data rely on external resources?_

    The _wordids_ used in this data are adopted from Gorman et al. (2018) and can be connected with an IPA representation of the pronunciation using the wordids.tsv located in the WikipediaHomographData repo, [here](https://github.com/google/WikipediaHomographData/blob/master/data/wordids.tsv).

4. _Are there recommended data splits or evaluation measures?_

    The data is provided in splits that force the presence of at least one of each homograph pronunciation in addition to providing an otherwise stratified representation of the pronunciations. This rectifies an issue in the Wikipedia Homograph Data in which 20% of the pronunciations are not present in the evaluation set. While pronunciation presence is ensured in training, dev, and test sets, low prevalence of pronunciations still provide issues in the test set. In the original WHD, another 20% of the pronunciations only have 1 sample in the eval set, and about 30% of the pronunciations only have 1 sample in the test set provided in this data. Why is this problematic? Think about giving a student a test with only one, very specific question on a particular topic---the answer to said question certainly doesn't provide comprehensive proof of the student's knowledge concerning that topic. While leave-one-out evaluation has been used on the original WHD eval set (see Nicolis and Klimkov, 2021; [here](https://www.amazon.science/publications/homograph-disambiguation-with-contextual-word-embeddings-for-tts-systems)), this technique certainly does not rectify the problems presented by 40% of the WHD pronunciation classes have either 0 or 1 samples in the evaluation set. Further work in developing a larger, more comprehensive test set for this data is advised. Also, given the inherent imbalance in class sizes (within the original WHD the median homograph pronunciation class size ratio is 88 to 12 samples), it is recommended that micro, macro (or balanced) and per class accuracies be used in model evaluation. 

5. _What experiments were initially run on this dataset?_

     In the dissertation associated with this data, different machine learning models (multinomial logistic regression, ALBERT, BERT, and XLNet) are developed using the each of the test, test_euro, test_swbd, and test_swbd_hand datasets, and the dev dataset, and metrics are reported on the test dataset. The purpose of this experimentation is to determine if label imputation techniques generate data which, when used to augment human-labeled training data, improve model performance.

## Data collection process
1. _How was the data collected?_

     This question is answered in three subsections---the first covers the Wikipedia Homograph Data, the second explains the label imputation process by which the labeled data from the Switchboard (SWBD) corpus was generated, and the third explains the label imputation process which was used to generate labeled data from the French English Europarl data.

    **WHD**
    The WHD was obtained by a team at Google from Wikipedia text using a random sampling procedure to obtain sentences homographs that were subsequently labeled for pronunciation by 4 annotators. This data was made publically available through Github and subsequently used in dissertation experimentation. The original WHD contains two splits, train and eval, with 162 unique homographs, 308 distinct pronunciations, and around 16,000 samples. For this version of the WHD, the two splits are combined. Data associated with 34 of those homographs is then removed due to either invariance in pronunciation or prohibitively low sample size. The remaining data is split into three splits in order to develop models using a train and dev set, and provide performance metrics on a test set.

    I. Invariant homographs 
    
    Seventeen of the 34 homographs have only one pronunciation in the data set. These invariant homographs are removed so as not to bolster model scores, as the models would correctly pick the only class available. They are as follows:

    1.  addict
    2.  compact
    3.  consort
    4.  desert
    5.  elaborate
    6.  entrance
    7.  incense
    8.  instrument
    9.  interchange
    10.  intimate
    11.  job
    12.  moped
    13.  mouth
    14.  object
    15.  pigment
    16.  row
    17.  subject

    II. Prohibitively low resource homographs
    Data for a further seventeen homographs is then removed as these homographs include at least one pronunciation class with fewer than three instances. These homographs are as follows:

    1.  abstract
    2.  appropriate
    3.  august
    4.  bologna
    5.  conglomerate
    6.  console
    7.  contract
    8.  contrast
    9.  deviate
    10.  expatriate
    11.  house
    12.  jesus
    13.  mate
    14.  moderate
    15.  predicate
    16.  project
    17.  ravel

    After the removal of data for the 34 homographs, the remaining Wikipedia Homograph Data contains 128 unique homographs, 256 unique pronunciations and 12,714 samples. **34 from the remaining 128 homographs are selected as the focus of the empirical investigation on label imputation in the dissertation, and are represented in this dataset.** The homographs are chosen due to the following characteristics: 1) they exhibit greater-than-not class imbalance, with a median imbalance in the set of 88:12 samples per pronunciation, and 2) additional data was found in the French English Europarl datset for them. These homographs are as follows: 

    1. abuse
    2. abuses
    3. advocate
    4. alternate
    5. approximate
    6. associate
    7. close
    8. confines
    9. conflict
    10. construct
    11. content
    12. contest
    13. convict
    14. degenerate
    15. delegate
    16. deliberate
    17. lead
    18. live
    19. lives
    20. misuse
    21. perfect
    22. pervert
    23. present
    24. produce
    25. protest
    26. record
    27. recount
    28. separate
    29. supplement
    30. suspect
    31. upset
    32. uses
    33. wind
    34. winds

    Each of these 34 homographs has 2 pronunciations which makes for a total of 68 pronunciation classes represented across 3,407 samples split into 80% train, 10% dev, 10% test. See the Dataset composition section above for more.

    **SWBD**
     A subset of 20% of the NXT Switchboard dataset (LDC data set ID: LDC2009T26) was text-audio aligned and culled for sentences containing any of the 34 WHD homographs used in the dissertation. 20% of the NXT data was used due to time constraints. The following steps are taken to process the SWBD data and obtain IPA representations which are then mapped to WHD labels. Switchboard sphere-formatted audio files (LDC SWBD data set ID: LDC97S62) are converted to wave files with a set sampling rate of 16,500 and 16 bits per sample. This conversion is accomplished using the Sound eXchange, or SoX, library (Barras, 2012). The NXT Switchboard transcription data is formatted in NITE XML (Carletta et al., 2005), and  the ElementTree library (Lundh, 2008) is used to navigate the data and obtain the time stamps necessary to split the  transcriptions and recordings into sentences. Switchboard audio segments that contain one sentence each are determined using the sentence-initial and -final phonword pointers to times in the NXT SWBD audio. The audio is then split using the splitterkit software released by Jiang (2016). Sentences with WHD homograph presence are selected, and aligned with the audio file segments to create one-to-one sentence-level text and audio data. ARPAbet phonetic representations (Rabiner and Juang, 1993) of each word in the sentences are then obtained with the Montreal Forced Aligner (McAuliffe et al., 2017), and converted into IPA representations via arpa2ipa (Keller, 2018) for comparison against the WHD IPA representations. This process yields 2,935 automatedly labeled homograph instances with sentential context, and the samples contain 95 unique WHD homographs. From those samples, a subset of the sentences containing one of the 34 WHD homographs are selected, unique imputed representations are coupled with WHD IPA representations for each presentation, and a mapping between acceptable pronunciations is made which is used to then semi-automatedly label homographs in SWBD sentences with WHD labels. Due to the constraint of using only 34 WHD homographs, and a great deal of unmappable imputed labels, 147 labeled samples are generated this way. For more on this process, the first recorded attempt of its kind, see the _Label imputation from transribed audio_ section in the slide deck uploaded to this repo, and chapter four of the associated [dissertation]().

    During the above process, the researcher hand-labeled the 2,891 samples, 322 of which belong to the subset of the 34 homographs pertinent to the study, and these samples are used to augment the WHD train split in the train_swbd_hand data. The complete set of hand-labeled samples are recorded in swbd_hand_all.tsv. 

    **French English Europarl**
    To create the semi-automatedly imputed data for the train_euro data split, the  English  and  French,  sentence-aligned  Europarl corpus (Koehn, 2005) is culled to 202,968 parallel sentences (about 10% of the total corpus), each of which include a WHD homograph. Of those 202,968 up to 50 samples for each homograph are randomly sampled and 1,804 of those are word aligned using Jalili Sabet et al.’s (2020) SimAlign algorithm. The samples are then hand-labeled with WHD pronunciation labels by the researcher. During the manual labeling, a token alignment check is performed to determine the accuracy of theSimAligninterlingual token alignment, and inaccurate mappings are thrown out. The accuracy check was done using personal knowledge of French, and by referencing Google Translate when questions arose. Finally, French tokens found to align with WHD homographs that have a sense or sense set for which a WHD homonym pronunciation label is not available are also discarded. In the end, labeled samples for about 70% of the homographs in the final WHD splits are obtained, and 34 of those homographsare found to meet the condition of having one low prevalence pronunciation class. This determined the set of 34 homographs used throughout the study. A dictionary is made from the manual mapping of English homograph pronunciations and aligned French text word forms. The dictionary is used to automatedly label the rest ofthe 202,968 sentences found to include a WHD homograph. Because some aligned French tokens are mapped to multiple homographs, the keys of the dictionary consist of each English homograph and French token pair, while the values contain the pronunciation label for theEnglish homograph. Using the dictionary in a script, aligned French and English homograph tokens are checked against the keys, and labeled samples are generated. The labeling implementation was applied to roughly 25% of the Europarl French-English corpus and yielded 8,656 labeled instances of 66 English homographs (30 of which have samples for multiple pronunciations). A subset of 1,308 of the imputed samples have homographs with one low prevalence class, and are selected to augment the WHD training data set. While about 50% of  the aligned token to class pairs have imputed labels for ten or fewer samples, there are pairs with large numbers of samples, the largest sample size being 220 for the homograph, _present_, with the aligned token, _présenter_, and the label /ˌpɹi:'zɛnt/. In order not to introduce too great an imbalance into the data (though ideally the sample sizes would remain reflective of the actual underlying distributions), up to forty samples for each French alignment to homograph pronunciation pair are randomly selected from the data to be used for augmentation. Note that this does not restrict the number of imputed label samples to 40 per pronunciation classas there can be multiple alignment pairs per pronunciation. This subsampling affects four homographs, _close_, _live_, _lives_, and _present_ with eight alignment pairs:  one for _close_, three for _live_, two for _lives_, and two for _present_.  After subsampling, 718 label-imputed samples targeting low resource pronunciation classes are obtained with a minimum of one sample per class, and a maximum of 130 samples per class. About 50% of the data has 10 or fewer samples per class. To create the augmented training data split, the label-imputed data is shuffled in to a copy of the 34-homograph WHD train split. This increases the train data set size by about 20%, from 2,719 to 3,437 samples. There is an absolute change in class sample size in the range of anywhere from 1 to 130 samples, with 75% of the classes gaining 21 samples or fewer. Themean relative change in sample size is 253%, with 75% of the classes increasing by 116% or less.

3. _Who was involved in the data collection process?_

    The researcher, Jen Seale, under the advisement of her dissertation committee at the CUNY Graduate Center: Professors William Sakas, Kyle Gorman, and Alla Rosovskaya.
5. _Over what time-frame was the data collected?_

    The data was collected over a few months time in 2021.
7. _How was the data associated with each instance acquired?_

    Through hand-labeling and label imputation.
8. _Does the dataset contain all possible instances?_

    No. All of the labeled data is subset, either to alleviate invariance, to redudce imbalance, or to reduce the problem space to clearly examine the effects of training with label-imputed data.
10. _If the dataset is a sample, then what is the population?_

    The population is the complete the Wikipedia Homograph Data, combined with the unknown number of sentences in both the NXT Switchboard data and French English Europarl data that contain WHD homographs.
12. _Is there information missing from the dataset and why?_

    Due to time and computation constraints, smaller amounts of data are generated during label imputation. Augmenting with this smaller amount of data still shows an increase in model balanced accuracy and provides evidence towards the efficacy of the label imputation techniques.
14. _Are there any known errors, sources of noise, or redundancies in the data?_

    At the time of writing, there are six known errors in the WHD for the homograph, _close_. In addition, all of the labels for the homograph _pasty_ in the WHD should be switched, the one pronunciation for the other. Fixes for these errors will be pushed to the Wikipedia Homograph Data GitHub repo.

    The following samples for _close_ are all labeled with close_adj-nou, and should be labeled with close_vrb: 

    "NXP Semiconductors And Freescale Semiconductor Close Merger RTTNews."
    "This time, Woolley held his side together with 76 and they were 172 for five at the close."
    "In June 2010 it was announced that the Museum would close because of Lincolnshire County Council cuts."
    "Ferrybridge and Eggborough power stations were scheduled to close in March 2016."
    "Blue doors close at the end of the current wave of enemies, but green ones remain open."
    "At the close of each episode, Burnett tugged her ear."
    "In May 2010 Cardiff University announced that the Academy would close because of a lack of funds."
    "It is designated "Trap Town" at the close of the 18th century by Griffith (1795)."

    Some of the errors are possibly made in part due to the use of POS in the wordid/labeling convention. The label _close_adj-nou_ indicates the /'kloʊs/ pronunciation, while the label _close_vrb_ indicates the /'kloʊz/ pronunciation.

## Data preprocessing
1. _What preprocessing/cleaning was done?_

    While preprocessing to this data is done in order to create feature vectors and token classification data for the logistic regression and transformer models, respectively, it is archived here in its base form. Of note, the Switchboard samples are provided without capitalization, as they are in the original data. For the purposes of modeling with logistic regression, BERT, and XLNet the data went through a process of capitalization rehydration. 
    
2. _Was the "raw" data saved in addition to the preprocessed/cleaned data?_

    Yes, this dataset constitutes the "raw" data.
4. _Is the preprocessing software available?_

    There are not plans to make the code available. 
6. _Does this dataset collection/processing procedure acheive the motivation for creating the dataset?_

    Yes. Homograph disambiguation modeling with this dataset provideds

## Data distribution
1. _How is the dataset distributed?_

    This data is redundantly archived in the [CUNY Graduate Center Creative Commons repository](), in addition to this GitHub repo. The dissertation associated with this work is available [here]().  
2. _When will the dataset be released/first distributed?_

    This dataset will first be released in September 2021.
4. _What license (if any) is it distributed under?_

    This dataset is distributed under the MIT license.

## Dataset maintenance
1. _Who is supporting/hosting/maintaining the dataset?_

    You can contact Jen for support via email: jennifer.seale@gmail.com.
2. _Will the dataset be updated?_

    There are no current plans for this data to be updated. Fixes to, and rebalancing of, the Wikipedia Homograph Data are being pushed to a fork of the Google WikipediaHomographData repo, [here](https://github.com/google/WikipediaHomographData).
3. _If the dataset becomes obsolete how will this be communicated?_

    This dataset is an artifact associated with work done for a dissertation, and in that respect will not become obsolete. Should better metadata or additional related datasets become available, it will be communicated in this GitHub repo on this README.
4. _Is there a repository to link to any/all papers/systems that use this dataset?_

    At this time only one paper, the dissertation, has used this dataset. There is no current plan to link all papers and systems that may use this dataset in future.

5. _If others want to extend/augment/build on this dataset, is there a mechanism for them to do so?_

    Sure. Fork this repo.

## Legal & ethical considerations
1. _If the dataset relates to people (e.g., their attributes) or was generated by people, were the informed about the data collection?_

    All of this language data has been generated by people. The people who wrote the Wikipedia articles are aware that their information is publically available to researchers, though their extent of knowledge as to the use of their text for the purpose of developing machine learning models must vary widely. The participants in the Switchboard experiments were informed, and the European parliment speakers were aware their speech was public.
2. _If it related to other ethically protected subjects, have appropriate obligations been met?_

    This data is not related to ethically protected subjects. 
4. _If it relates to people, where there any ethical review applications/reviews/approvals?_

    As this data was all publically available, no ethical reviews were performed.
5. _If it relates to people, were they told what the dataset would be used for and did they consent? What community norms exist for data collected from human communications?_

    The subjects who generated this language data were not aware their data would be used for the purpose of developing pronunciation label imputation techniques or machine learning models for homograph disambiguation. It is a community norm to use publically available data such as the WHD, SWBD, and Europarl data for many different NLP tasks. 
6. _If it relates to people, could this dataset expose people to harm or legal action?_

    No.
7. _If it relates to people, does it unfairly advantage or disadvantage a particular social group?_

    Not to the researcher's knowledge.
8. _If it relates to people, were they provided with privacy guarantees?_

None of this anonymized data was collected by the researcher from people---only already publically available datasets were used. For more, see the papers referenced at the top of this README.

9. _Does the dataset comply with the EU General Data Protection Regulation (GDPR)?_

    Unknown.
10. _Does the dataset contain information that might be considered sensitive or confidential?_

    No.
11. _Does the dataset contain information that might be considered inappropriate or offensive?_

    There is text which contains general information, personal opinion, or fictional text surrounding potentially sensitive topics such as abuse, affairs, and prison. Nothing is explicit, inappropriate or offensive to the researcher's knowledge.

## References

Barras, Benjamin (2012). “SoX : Sound eXchange”. URL:http://sox.sourceforge.net/.

Carletta, J. et al. (2005). “The NITE XML toolkit: Data model and query language”. In:Language Resources and  Evaluation 39, 313–334.DOI:https://doi.org/10.1007/s10579-006-9001-9.

Godfrey, John J., Edward C. Holliman, and Jane McDaniel (1992). [SWITCHBOARD: Telephone speech corpus for research and development](https://dl.acm.org/doi/10.5555/1895550.1895693). In: Acoustics, Speech, and Signal Processing, IEEE International Conference on. Vol. 1. IEEE Computer Society, pp. 517–520

Gorman, K., Mazovetskiy, G., and Nikolaev, V. (2018). [Improving homograph disambiguation with machine learning](https://aclanthology.org/L18-1215/). In Proceedings of the Eleventh International Conference on Language Resources and Evaluation, pages 1349-1352. Miyazaki, Japan.

Jiang, Andrew (2016). Wav Splitter API, Splitterkit. URL:https://github.com/abvthecity/wav-splitter-library.

Keller, O. (2018). arpa2ipa. URL:https://phoenix.yizimg.com/chorusai/arpa2ipa.

Koehn, Philipp (2005). [Europarl: A parallel corpus for statistical machine translation](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.459.5497&rep=rep1&type=pdf). In: Citeseer. 

Lundh, Fredrik (2008). ElementTree toolkit. URL:https://github.com/python/cpython/blob/3.9/Lib/xml/etree/ElementTree.py.

Masoud, Jalili Sabet et al. (Nov. 2020). “SimAlign: High quality word alignments withoutparallel training data using static and contextualized embeddings”. In:Proceedings ofthe 2020 Conference on Empirical Methods in Natural Language Processing: Findings.Online:  Association  for  Computational  Linguistics,  pp.  1627–1643.URL:https://www.aclweb.org/anthology/2020.findings-emnlp.147.

McAuliffe, Michael et al. (2017). “Montreal Forced Aligner: Trainable text-speech alignment using Kaldi.” In: Interspeech. Vol. 2017, pp. 498–502. URL: https://www.researchgate.net/profile/Morgan_Sonderegger/publication/319185277_Montreal_Forced_Aligner_Trainable_Text-Speech_Alignment_Using_Kaldi/links/59b84d450f7e9bc4ca393755/Montreal-Forced-Aligner-Trainable-Text-Speech-Alignment-Using-Kaldi.pdf.

Rabiner, Lawrence and Biing-Hwang Juang (1993). Fundamentals of Speech Recognition. Englewood Cliffs, New Jersey: Prentice-Hall.
