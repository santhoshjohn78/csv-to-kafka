# CSV to Kafka topic

This reads a csv file that is got from kaggle and loads it into a kafka
topic 

1. place the csv file in the unprocessed folder

The data csv is from kaggle that shows zillow homes in california

2. use postman and PUT this json to the URL http://localhost:8083/connectors/source-csv-spooldir-20/config

````
{
"connector.class": "com.github.jcustenborder.kafka.connect.spooldir.SpoolDirCsvSourceConnector",
"topic": "cali_housing_spooldir_20",
"input.path": "/data/unprocessed",
"finished.path": "/data/processed",
"error.path": "/data/error",
"input.file.pattern": ".*\\.csv",
"csv.null.field.indicator": "BOTH",
"schema.generation.enabled":"true",
"schema.generation.key.fields":"latitude,longitude",
"csv.first.row.as.header":"true",
"transforms":"castTypes",
"transforms.castTypes.type":"org.apache.kafka.connect.transforms.Cast$Value",
"transforms.castTypes.spec":"housing_median_age:float32,total_rooms:float32,total_bedrooms:float32,population:float64,households:float32,median_income:float32,median_house_value:float64,ocean_proximity:string"
}
````

This will create a connector that reads from the unprocessed folder and transforms to the spec that is mentioned. The schema that
is created in the schema registry will contain the data types mentioned in the spec.

3. You can check if the topic has got the message by running the following command

````
docker exec kafkacat kafkacat -b broker:29092 -t cali_housing_spooldir_20 -C -o -1 -c 5 -J -s key=s -s value=avro -r http://schema-registry:8081
````
This command reads the messages last but 1 note -o option has -1 which means last by one offset. -c has 5 which means 5 messages here 
it does not make sense.

### Important note
To make it work in windows I had to mount the absolute path of the data folder in the project to /data path in the kafka-connector container


## References & Acknowledgements

* https://rmoff.net/2020/06/17/loading-csv-data-into-kafka/
* https://docs.confluent.io/platform/current/connect/transforms/cast.html#cast-comma-separated-values
* https://docs.confluent.io/kafka-connectors/spooldir/current/connectors/csv_source_connector.html#tsv-input-file-example
* 
