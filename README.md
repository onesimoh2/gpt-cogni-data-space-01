 ![CogniLake01](https://github.com/onesimoh2/gpt-cogni-data-space-01/assets/99890486/f62f064c-9fbe-4cf2-baa8-e828dbfc7702)
 
# INTRODUCTION

The evolution of data manipulation is often depicted as a progression, moving from traditional databases to advanced data warehouses, and further evolving into the concept of data lakes. As someone deeply engaged in the creation, maintenance, and operation of these systems within large and complex organizations, I am aaware of the intricate challenges they entail. Beyond managing the Extract, Transform, Load (ETL) processes to ensure data integrity, there is the additional layer of data transformation workflows essential for fulfilling end-user needs, such as reporting, management, and analytical functionalities. 

Data lakes, housing vast amounts of diverse information, have undergone transformation by incorporating innovative concepts like Lakehouses, introducing some degree of structuring to enhance usability and the concept of data mesh has involving fragmentation and distribution strategies, making data lakes easier to maintain and utilize. 

However, harnessing the full potential of the data necessitates the expertise of skilled data analysts who craft intricate data transformation workflows. These workflows involve gathering data from various sources until the desired data set is obtained. 

The idea of automating the creation of these workflows has been a central theme throughout my career. From my PhD to other research endeavors, I delved into the concept of describing each data element with metainformation about its content and subsequently employing symbolic processes to attain this objective. This idea was influenced by various fields, including expert systems, data structures, predicate logic, and more. Among these influences, one work that holds particular significance is Bo Sundgren's 'An Infological Approach to Data Bases' from 1973. Sundgren's insights played a pivotal role in shaping my approach to addressing this challenge. 

Recent breakthroughs in LLM (Large Language Models) have shed light on the limitations of the previously mentioned ideas, which, while not entirely erroneous, have proven insufficient in tackling the intricacies of contemporary data manipulation. In response to these challenges, the concept of 'CogniLake' comes to the forefront. 

At its core, 'CogniLake' retains the fundamental notion of describing each data element with metainformation. However, it harnesses the power of LLM models to automatically generate the necessary workflows for data retrieval, extraction, transformation, and presentation in the desired format. This fusion of advanced language models with data management offers an exciting and innovative path towards streamlining and optimizing the entire data manipulation process, opening new possibilities for enhanced data utilization, and understanding. 

The algorithm's concept unfolds as follows: it commences with a query that articulates the required data, followed by a process of identifying all relevant data sets within the data lake. A potential method to achieve this involves transforming the metadata of all data in the data lake into embedded vectors, storing them in a vector database. Additionally, the algorithm converts the query into an embedded vector and initiates a search to pinpoint the most relevant elements within the data lake. Having gathered all potential elements, the LLM will rank them based on their relevance in fulfilling the given query. Once this ranking is established, the LLM is then tasked with generating the necessary workflow to present the data precisely as requested. 

Undoubtedly, the outlined algorithm presents a high-level concept that demands further precision and detailed elaboration. To assess its potential advantages, I have undertaken the task of creating a proof of concept. Although this initial demonstration may be limited in scope, it provides valuable insights into the algorithm's functionality and pathways for improvement. Through this experimental phase, I aim to gain practical experience, identify any existing flaws, and develop a deeper understanding of the project's scope and complexity. 

Despite the limitations inherent in the proof of concept (POC), it has successfully generated Python scripts connecting the various data components culminating in the creation of the ‘dataframe’ sought by the query. As we contemplate the possibility of achieving such results with GPT-4, it becomes apparent that even greater advancements could be expected in subsequent iterations of the GPT model. This line of thinking serves as a validation of the feasibility of implementing the 'CogniLake' concept. The potential for future improvements in language models reinforces the notion that 'CogniLake' holds promise in revolutionizing data manipulation and streamlining workflows, driving us towards more intelligent and efficient data management solutions. 

# DESCRIPTION OF THE PROGRAM

There are two things that are omitted in the program. One is the initial search for related documents to the query and the other is executing the generated Python script. I have examples of how to do both things in other projects of this same repository. 

So I’m assuming the existence of three files that are needed to complete the initial query. For those files, their metadata were defined and added to the list called metadata_lst. 

The whole algorithm is founded on the concepts of query, content, and value. The query is the request that is made to the system, in this case I use plain text. The content shows what is the data stored in the data sources at a given moment and the value is a rank that we ask GPT-4 to calculate showing the relevance of each data source to the query at a given moment. 

For each iteration, a data source is selected and then GPT-4 is asked to generate a Python script to perform dataframe operations to systematically construct the final requested content expressed in the query. For this the calling function feature is used. 

I encourage to look at the program and especially at the prompts to really understand, more precisely, the algorithm. 

# RESULTS

The query is requesting fish capture monthly data containing years 1986 to 2012 and columns Date, Total and weight. But since there is no data source containing this information, the program tries to build it from the real existing ones. 

## FIRST ITERATION 

In the first iteration the file named catfish-Allyears-onlywheight.csv 

The generated script result was: 

import pandas as pd 
#Read the csv file into a dataframe 
df2 = pd.read_csv('data/catfish-Allyears-onlywheight.csv')

#Filter the dataframe to only include years from 1986 to 2012 
df2['Date'] = pd.to_datetime(df2['Date']) 
df2 = df2[(df2['Date'].dt.year >= 1986) & (df2['Date'].dt.year <= 2012)] 
#Save the dataframe to a csv file 

df2.to_csv('Fish_all_data.csv', index=False) 

## SECOND ITERATION 

In the second iteration the file named catfich-from_2000.csv 

The generated script result was: 

import pandas as pd 

#Read the dataframes from the csv files 
fish_all_data = pd.read_csv('Fish_all_data.csv') 
data_from_2000_on = pd.read_csv('data/catfich-from_2000.csv') 

#Convert the 'Date' column to datetime format 
fish_all_data['Date'] = pd.to_datetime(fish_all_data['Date']) 
data_from_2000_on['Date'] = pd.to_datetime(data_from_2000_on['Date']) 

#Set 'Date' as the index for both dataframes 
fish_all_data.set_index('Date', inplace=True) 
data_from_2000_on.set_index('Date', inplace=True) 

#Join the dataframes 
result = fish_all_data.join(data_from_2000_on, how='outer') 

#Save the resulting dataframe to a csv file 
result.to_csv('Fish_all_data.csv') 

## FINAL ITERATION 

In the finaliteration the file named catfich-until1999.csv 

The generated script result was: 

import pandas as pd 

#Read the dataframes from the csv files 
df1 = pd.read_csv('Fish_all_data.csv') 
df2 = pd.read_csv('data/catfich-until1999.csv') 

#Ensure that 'Date' is of datetime type in both dataframes 
df1['Date'] = pd.to_datetime(df1['Date']) 
df2['Date'] = pd.to_datetime(df2['Date']) 

#Perform an outer join on the 'Date' column 

df1 = df1.set_index('Date').join(df2.set_index('Date'), how='outer', lsuffix='_df1', rsuffix='_df2') 

#Combine the 'Total' columns from both dataframes 
df1['Total'] = df1['Total_df1'].combine_first(df1['Total_df2']) 

#Drop the unnecessary columns 
df1 = df1.drop(columns=['Total_df1', 'Total_df2']) 

#Save the resulting dataframe to a csv file 
df1.to_csv('Fish_all_data.csv') 

## REMARKS

I manually run those scripts to verify they were correct. It was not an easy task but after a lengthy process of trial and error I managed to define the prompts in a way that GPT-4 understood. The result are prompts that are verbose and self-repeating and I’m convinced that it is possible to achieve the same result with more condensed prompts. While the result is fine it is no guaranty it will run for every combination of files. It is necessary to define the content in a more formal and precise way, so every step knows what it should do. I must revise old ways of declaring metainformation and take ideas from it. In general, this is a proof of concept that is extremely limited but very useful to show me the possibility of the concept. 

## CONCLUSION

I like to think of CogniLake not in the context of the current GPT-4 but in its further versions. I expect GPT-5 and over to be more accurate in generating Python code and in its ‘internal reasoning’ about what is being asked. If with current means the system produced satisfactory results with better models, I expect to be able to automate even complex workflows. 

These results and the future improvements in LLMs assure me that that the concept of CogniLake is not only achievable but necessary for the future of data manipulation. 