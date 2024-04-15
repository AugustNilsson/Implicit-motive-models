# Implicit-motive-models
Our methodology for implicit motive coding is implemented within the CRAN package text (Kjell et al., 2023). The following workflow can be implemented to automatically code stories from the Picture Story Exercise for the implicit motives of power, achievement, and affiliation using the models and procedure presented in this paper (See Figure A1 for a visualization of the framework):



#### Initial setup: Install and open the text package in an R environment (only required the first time). 
install.packages("text")
textrpp_install()
textrpp_initialize()
library(text)

#### 1: Load data. The following data serves as an example:  
data <- dplyr::mutate(.data = Language_based_assessment_data_8, participant_id = dplyr::row_number(), story_id = rep(1:5, each = 8)) 

#### 2: Retrieve sentence, story, and person-level predictions. Choose between our three models: “power”, “achievement” or “affiliation”.  
predictions <- textPredict(texts = data$satisfactiontexts,
                           model_info = "power",
                           participant_id = data$participant_id,
				   story_id = data$story_id,
   dataset_to_merge_predictions= data)

#### 3: Examine sentence, story, and person-level predictions. 
predictions$sentence_predictions
predictions$person_predictions
predictions$story_predictions
predictions$dataset 

#### Necessary Parameters:
#texts: List of implicit motive language. Can be in the form of sentence-, story- or person-level.

#model_info: Reference to a pre-trained model (GitHub URL containing the keyword: ‘implicit_motives’, or ‘power’, ‘achievement’, or ‘affiliation’ for our current top-performing models).

#### Necessary with at least one out of “participant_id” and “story_id”
#participant_id: Vector of participant-ids. Specify this for getting person level scores (i.e., summed sentence probabilities to the person level corrected for word count). 

#story_id: Vector of story-ids. Specify this to get story-level scores (i.e., summed sentence probabilities corrected for word count). When there is both story_id and participant_id indicated, the function returns a list including both story level and person level prediction corrected for word count. 

#### Optional parameters

#dataset_to_merge_predictions: (optional) Your dataset where you want to allocate the model-based predictions. Include this to incorporate person, story-level predictions and/or sentence-level predictions into the original dataset.  

#previous_sentence: If set to TRUE, word embeddings will be averaged over the current and previous sentence per story-id. For this, both participant-id and story-id must be specified. 

#save_embeddings: By default, embeddings will be stored with a unique identifier, ensuring that new embeddings are only generated once for the same text (unless save_embeddings is set to FALSE).

#save_dir: By default, embeddings will be saved in the current work directory, unless ‘save_dir’ is set to a different directory, e.g., ‘new/path’. 

#save_name: (optional) Embeddings will be saved as ‘textPredict_unique_identifier.rds’, unless ‘save_name’ is specified.

#### Output Objects:
#predictions: Returned by textPredict as 1) a data frame of sentence level probabilities and classifications if no participant_id or story_id are defined, 2) as a list with two or three tibbles (i.e., data frames), including sentence and person, and/or story level predictions. If the ‘dataset_to_merge_predictions’ parameter is defined, the list will also contain the original dataset with the predictions inserted.  

#### The Algorithm - Correcting for Word Length:
#Texts provided to the ‘texts’ parameter are automatically separated by full stops, question- and exclamation marks. Exceptions for full-stop separations include “Mr.”, “miss.”, and “Mrs.”.
#After splitting the data into sentences, each sentence is assigned a probability and a class by the model indicated in the parameter model_info. These are summed for each participant-id together with the number of words produced by each participant divided by 1000. Then, we square-root all sums and fit two linear models: both with summed words on x, one with summed probabilities on y, and the other with summed classifications on y. Residuals from these models are our person-level predictions. The same procedure is used for computing story-level predictions.
 
