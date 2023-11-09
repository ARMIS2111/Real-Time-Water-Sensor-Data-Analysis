# Real-Time Data Pipeline Using Kafka and Spark

## Data Pipeline Architecture

  

![image](https://github.com/ARMIS2111/real-time-water-temperature-data-pipeine/assets/101886753/2807d5c5-bc95-4b70-a07b-daaa437457a8)


-   ### API
   
	-  	 The API mimics the water quality sensor data similar to the one shared [here](https://data.world/cityofchicago/beach-water-quality-automated-sensors).
	    
	-   The implementation is done in flask web framework.

-   ### Kafka Producer (Topic: RawSensorData)
    
	
	-   The data from the API stream is pushed to Kafka Producer under topic: RawSensorData

-   ### Apache Spark and Kafka Consumer (Topic: CleanSensorData)
    

	-   The data under the topic RawSensorData is streamed through Kafka Consumer. The data is then structured and validated using Spark.
	    

	  

	-   The cleaned data is then pushed to MongoDB and Kafka Producer under topic: CleanSensorData

-   ### MongoDB
    

	-   The structured data is pushed to MongoDB collection with the following schema:
	    
		```markdown
		| Keys             | Data Type |
		|------------------|-----------|
		| _id              | Object Id |
		| Beach            | String    |
		| MeasurementID    | long      |
		| BatteryLife      | Double    |
		| RawData          | String    |
		| WaterTemperature | Double    |
		| Turbidity        | Double    |
		| TimeStamp        | timestamp |
		```  
  
  

-   ### Realtime Dashboard
    

	-   The dashboard is implemented in the bokeh visualization library and data is streamed using Kafka Consumer under topic CleanSensorData.
	    

		![image](https://github.com/ARMIS2111/real-time-water-temperature-data-pipeine/assets/101886753/e514b6cb-c06a-4734-a783-7afde124962e)


  

## How to run the code
 
If your kafka installation folder is not added to path, navigate to the directory and run the following commands
-   #### Start Zookeeper
    

		bin/zookeeper-server-start.sh config/zookeeper.properties
    

  
-   #### Start Kafka
    

		bin/kafka-server-start.sh config/server.properties

 

-   #### Create RawSensorData Topic
    

		   bin/kafka-topics.sh --create --topic RawSensorData --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1
    

  

-   #### Create CleanSensorData Topic
    

		    bin/kafka-topics.sh --create --topic CleanSensorData --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1
-  #### View RawSensorData Topic
    

		bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic RawSensorData --from-beginning
    

  

-   #### View CleanSensorData Topic
    

		bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic CleanSensorData --from-beginning
-  #### Start the MongoDB server 
    

  		 sudo systemctl enable mongod.service
-  #### Start the API (port: 3030)
    

  		 python sensor.py
  

-  #### Push Data From API Stream to Kafka Topic: RawSensorData
    

		python push_data_to_kafka.py
    

  

-   #### Structure and Validate Data, Push To MongoDB and Kafka Topic CleanSensorData
    

		spark-submit structure_validate_store.py
    

  

-   #### Real-Time DashBoard - Visualization
    

		bokeh serve --show dashboard.py
