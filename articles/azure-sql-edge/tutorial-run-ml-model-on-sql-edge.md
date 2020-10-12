---
title: Wdrażanie modelu ML w usłudze Azure SQL Edge przy użyciu ONNX
description: W trzeciej części tego przykładowego samouczka usługi Azure SQL Edge na potrzeby przewidywania zanieczyszczeń żelaza można uruchamiać modele uczenia maszynowego ONNX w usłudze SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: abd1bc9b53c303d094d74683da0d6e3fad4d715f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90887463"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Wdrażanie modelu ML w usłudze Azure SQL Edge przy użyciu ONNX 

W trzeciej części tego samouczka z trzema częściami w celu przewidywania zanieczyszczeń żelaza w usłudze Azure SQL Edge:

1. Użyj Azure Data Studio, aby nawiązać połączenie z SQL Database w wystąpieniu usługi Azure SQL Edge.
2. Przewidywanie zanieczyszczeń żelaza z ONNX w usłudze Azure SQL Edge.

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>Nawiązywanie połączenia z SQL Database w wystąpieniu usługi Azure SQl Edge

1. Otwórz narzędzie Azure Data Studio.

2. Na karcie **Zapraszamy** Rozpocznij nowe połączenie z następującymi szczegółami:

   |_Pole_|_Wartość_|
   |-------|-------|
   |Typ połączenia| Microsoft SQL Server|
   |Serwer|Publiczny adres IP wymieniony na maszynie wirtualnej, który został utworzony dla tej wersji demonstracyjnej|
   |Nazwa użytkownika|sa|
   |Hasło|Silne hasło użyte podczas tworzenia wystąpienia usługi Azure SQL Edge|
   |baza danych|Domyślne|
   |Grupa serwerów|Domyślne|
   |Nazwa (opcjonalnie)|Podaj nazwę opcjonalną|

3. Kliknij przycisk **Połącz** .

4. W sekcji **plik** Otwórz nowy Notes lub użyj skrótu klawiaturowego Alt + Windows + N. 

5. Ustaw jądro na Python 3.

## <a name="predict-iron-ore-impurities-with-onnx"></a>Przewidywanie zanieczyszczeń żelaza przy użyciu ONNX

Wprowadź następujący kod w języku Python w notesie Azure Data Studio i uruchom go.

1. Najpierw zainstaluj i zaimportuj wymagane pakiety.

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. Zdefiniuj konfigurację eksperymentu obszaru roboczego usługi Azure AutoML i AutoML na potrzeby eksperymentu regresji.

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. Zaimportuj zestaw danych do ramki Panda. Na potrzeby szkolenia modelu należy użyć prognozowania jakości zestawu danych szkoleniowych [w procesie wyszukiwania](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process) z Kaggle. Pobierz plik danych i Zapisz go lokalnie na komputerze deweloperskim. Te dane będą używane do przewidywania, jak dużo zanieczyszczeń jest w koncentracji rudy.

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. Analizuj dane, aby identyfikować dowolną skośność. W trakcie tego procesu należy przyjrzeć się dystrybucji i pochylić informacje dla każdej kolumny w ramce danych.

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. Sprawdź i popraw poziom skośności danych.

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. Sprawdź korelację innych funkcji za pomocą funkcji przewidywania. Jeśli korelacja nie jest wysoka, usuń te funkcje.

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. Rozpocznij eksperyment dotyczący uczenia maszynowego, aby znaleźć i szkolić najlepszy algorytm. W takim przypadku testuje się ze wszystkimi algorytmami regresji z podstawową metryką znormalizowanego średniego środka o wartości kwadratowej (NRMSE). Aby uzyskać więcej informacji, zobacz [Podstawowa metryka badania platformy Azure ml](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric). Poniższy kod rozpocznie lokalne uruchomienie eksperymentu ML.

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. Załaduj model w bazie danych usługi Azure SQL Edge na potrzeby lokalnego oceniania.

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. Na koniec użyj modelu usługi Azure SQL Edge do wykonywania prognoz przy użyciu przeszkolonego modelu.

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. Przy użyciu języka Python Utwórz wykres wartości procentowej dla przewidywanej krzemionki dla źródła danych żelaznych, DateTime i krzemionki.

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z modeli ONNX w usłudze Azure SQL Edge, zobacz temat [Uczenie maszynowe i AI z ONNX w programie SQL Edge](onnx-overview.md).
