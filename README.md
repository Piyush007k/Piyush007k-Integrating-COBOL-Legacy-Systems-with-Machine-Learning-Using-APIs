# Integrating-COBOL-Legacy-Systems-with-Machine-Learning-Using-APIs
## Introduction

![IMG](https://datastorageasean.com/sites/default/files/images/GettyImages-1206796363.jpg)
In today's digital landscape, the integration of cutting-edge technologies with legacy systems presents both challenges and opportunities. Here we discuss technique that enables the seamless integration of machine learning capabilities within the COBOL programming language. 

Imagine a scenario where a client's database is written in COBOL, requiring access to data for fraud detection purposes. This is precisely the situation where leveraging machine learning within COBOL becomes imperative. 



## What is FastAPI? 
![IMG](https://miro.medium.com/v2/resize:fit:1023/1*du7p50wS_fIsaC_lR18qsg.png)

  FastAPI is a web framework for building APIs with Python 3.7+ based on standard Python type hints. It is built on top of Starlette for the web parts and Pydantic for the data        parts. FastAPI is designed to be easy to use, fast to develop with, and highly performant, making it an ideal choice for building robust APIs. 

  **Getting Started**
  
  To begin with FastAPI, you'll need Python 3.7 or higher installed on your system. You can install FastAPI and Uvicorn, the ASGI server, using pip:
    
    pip install fastapi uvicorn 
    
  **Creating Your First FastAPI Application Developing a Basic FastAPI Application with Integrated Machine Learning Functionality:** 

    from fastapi import FastAPI , HTTPException,Request 
    import uvicorn 
    app = FastAPI() 
    from joblib import load 
    import pandas as pd 
    import warnings 
    import xgboost as xgb   
    warnings.filterwarnings('ignore') 

     #v2_xgBoost_DC_mode.joblib 
    scalar_dc = load("dc_scalar.joblib") 
    model_dc = load("xgBoost_DC_mode_new2.joblib") 
    
    @app.get("/") 
    def read_root(): 
        return {"Welcome to ML Model API "} 
    
    @app.post("/mlPredictionModel") 
    def process_model_dc(data:dict ): 
            assert len(data) == 4, "Please pass all 4 required parameters"     
            return_dict = process_dc_model_cobol(data) 
            return return_dict 
            
    def process_dc_model_cobol(data): 
    
            column_1 = int(data.get("column_1")) 
            column_2 = int(data.get("column_2")) 
            column_3 = int(data.get("column_3")) 
            column_4 = int(data.get("column_4")) 
            
           test_frame = pd.DataFrame([[column_1,column_2,column_3,column_4]],columns=[column_1,column_2,column_3,column_4])  
           
    scaled_test_frame = scalar_dc.transform(test_frame)         
    result = model_dc.predict(scaled_test_frame) 
    
    if __name__ == "__main__": 
    
        uvicorn.run("main:app",host="0.0.0.0" ,port=8000,workers = 1, timeout_keep_alive =600)

So this is the short cod where scaler variable and Ml Model from joblib file and integrated it in fast api.

## What is COBOL? 
![IMG](https://www.opensourceforu.com/wp-content/uploads/2021/03/Cobol-classical-programming_Featuree-image-2.jpg)

  COBOL, which stands for Common Business-Oriented Language, is a high-level programming language primarily used for business, finance, and administrative systems. It was developed in the late 1950s and early 1960s, making it one of the oldest programming languages still in use today. COBOL is known for its readability and structured programming features, making it suitable for large-scale enterprise applications. 

  **How to use COBOL to call FAST API**
  
  COBOL is an older language and may not have built-in support for making HTTP request, so here we used cURL utility which is command-line utility. That can make HTTP request.	You can use “ CALL ‘SYSTEM’ “ statement to provide a way for COBOL 	program to interact with OS environment and execute command on 	terminal. 
Optionally,the “RETURNING” statement can be used with “ CALL ‘SYSTEM’ “ to capture the return status of the command execution. We can directly display or store the variable in test file for futher manipulation. 

**Lets write a small COBOL program:** 

    IDENTIFICATION DIVISION. 
    PROGRAM-ID. CallFastAPI. 
    DATA DIVISION. 
    WORKING-STORAGE SECTION. 
    01 JSON-PAYLOAD PIC X(100) VALUE "{\"column1\":\"value1\",\"column2\":\"value2\",\"column3\":\"value3\",\"column4\":\"value4\"}" . 
    01 CURL-COMMAND PIC X(300) VALUE "curl -X POST -H 'Content-Type: application/json' -d '". 
    01 CURL-STATUS PIC 9. 
     
    PROCEDURE DIVISION. 
    BEGIN. 
        STRING JSON-PAYLOAD " http://192.158.152/ mlPredictionModel" INTO CURL-COMMAND 
        CALL 'SYSTEM' USING CURL-COMMAND 
                         RETURNING CURL-STATUS. 
        IF CURL-STATUS NOT EQUAL 0 
            DISPLAY "Fraud Detected: " CURL-STATUS 
        ELSE 
            DISPLAY "No Fraud Detected: " CURL-STATUS 
        END-IF. 
        STOP RUN. 
    END PROGRAM CallFastAPI. 

 

 

**The program will form cURL Command something just like this:** 

    curl -X POST -H 'Content-Type: application/json' -d "{\"column1\":\"value1\",\"column2\":\"value2\",\"column3\":\"value3\",\"column4\":\"value4\"}" http://192.158.152/mlPredictionModel 

    

## Diagramatic Representation Of Working:

![ML_COBOL1](https://github.com/Piyush007k/Piyush007k-Integrating-COBOL-Legacy-Systems-with-Machine-Learning-Using-APIs/assets/105144617/56ecfe92-2635-458c-9098-10a8c48fd08d)

It can be used with any API Fast API, Rest API etc, in this way their is direct connection between Cobol program and API using OS Environment. No third party tools or any third party Library are used everything is generic. So, you can prefer this.
