---
title: Używanie skalowania i platformy Spark z usługą Azure HDInsight
description: Używanie skalowania i platformy Spark na potrzeby manipulowania danymi i tworzenia modeli przy użyciu usług ML w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/26/2019
ms.openlocfilehash: c12398ceacf8495a05037422a6501dc8138abc10
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92628698"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Łączenie narzędzi do skalowania i platformy Spark w usłudze HDInsight

W tym dokumencie przedstawiono sposób przewidywania opóźnień przybycia lotów przy użyciu modelu regresji logistycznej **skalowania** . W przykładzie użyto opóźnień i danych pogody, które są sprzężone przy użyciu **platformy Spark** .

Mimo że oba pakiety działają w aparacie wykonywania Spark Apache Hadoop, są blokowane z udostępniania danych w pamięci, ponieważ każdy z nich wymaga odpowiednich sesji platformy Spark. Dopóki ten problem nie zostanie rozwiązany w przyszłej wersji ML Server, obejście tego problemu będzie obsługiwać nienakładające się sesje platformy Spark oraz wymieniać dane za pośrednictwem plików pośrednich. Instrukcje te pokazują, że te wymagania są bezpośrednie do osiągnięcia.

Ten przykład został początkowo udostępniony w porozmawiać o stratach 2016 przez Mario Inchiosa i Roni burd. Możesz to sprawdzić, [tworząc skalowalną platformę do nauki o danych w języku R](https://channel9.msdn.com/blogs/Cloud-and-Enterprise-Premium/Building-A-Scalable-Data-Science-Platform-with-R-and-Hadoop).

Kod został pierwotnie zapisany dla ML Server działającego w usłudze Spark w klastrze usługi HDInsight na platformie Azure. Jednak pojęcie dotyczące mieszania użycia platformy Spark i skalowania w jednym skrypcie jest również prawidłowe w kontekście środowisk lokalnych.

W procedurach przedstawionych w tym dokumencie przyjęto założenie, że masz pośredni poziom wiedzy o języku R i jest to biblioteka [skalowania](/machine-learning-server/r/concept-what-is-revoscaler) ml Server. W tym scenariuszu nastąpiło wprowadzenie do [platformy Spark](https://spark.apache.org/docs/2.1.0/sparkr.html) .

## <a name="the-airline-and-weather-datasets"></a>Zestawy danych linii lotniczych i pogody

Dane dotyczące lotu są dostępne z [archiwów rządowych Stanów Zjednoczonych](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). Jest on również dostępny jako plik zip z [AirOnTimeCSV.zip](https://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip).

Dane o pogodzie mogą być pobierane jako pliki zip w postaci nieprzetworzonej według miesiąca, z [krajowego repozytorium administracji oceanicznej i atmosferycznej](https://www.ncdc.noaa.gov/orders/qclcd/). Na potrzeby tego przykładu Pobierz dane dla maja 2007 – grudzień 2012. Użyj godzinnych plików danych i `YYYYMMMstation.txt` plików w ramach każdego z Zips.

## <a name="setting-up-the-spark-environment"></a>Konfigurowanie środowiska Spark

Użyj następującego kodu, aby skonfigurować środowisko Spark:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Następnie Dodaj `Spark_Home` do ścieżki wyszukiwania dla pakietów języka R. Dodanie go do ścieżki wyszukiwania umożliwia korzystanie z programu Spark i Inicjowanie sesji platformy Spark:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Przygotowywanie danych pogody

Aby przygotować dane pogodowe, podzbiór do kolumn wymaganych do modelowania: 

- Propagowan
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "WindSpeed"
- "Altimeter"

Następnie Dodaj kod lotniska skojarzony z stacją pogody i przekonwertuj miary z czasu lokalnego na UTC.

Zacznij od utworzenia pliku w celu zamapowania informacji o stacji pogody (WBAN) na kod lotniska. Poniższy kod odczytuje wszystkie godziny nieprzetworzonych plików danych pogody, podzestawy do kolumn, których potrzebujemy, Scala plik mapowania stacji pogody, dostosowuje daty pomiarów do czasu UTC, a następnie zapisuje nową wersję pliku:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Importowanie danych linii lotniczych i pogody do ramek dataframes

Teraz używamy funkcji Spark [. DF ()](https://spark.apache.org/docs/latest/api/R/read.df.html) do zaimportowania danych pogody i linii lotniczych do ramek dataframes. Ta funkcja, podobnie jak wiele innych metod Spark, jest wykonywana opóźnieniem, co oznacza, że są umieszczane w kolejce do wykonania, ale nie wykonywane do momentu, gdy są wymagane.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Czyszczenie i Przekształcanie danych

Następnie wykonamy pewne czynności w odniesieniu do danych linii lotniczych, które zostały zaimportowane, aby zmienić nazwy kolumn. Przechowujemy tylko zmienne, które są konieczne, i zaokrąglić zaplanowane przejściu w dół do najbliższej godziny, aby umożliwić scalanie z najnowszymi danymi pogody przy wyjściu:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Teraz wykonujemy podobne operacje na danych pogody:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Sprzęganie danych pogody i linii lotniczych

Teraz korzystamy z funkcji [joinobject ()](https://spark.apache.org/docs/latest/api/R/join.html) , aby wykonać lewe sprzężenie zewnętrzne danych linii lotniczych i pogodowych przez wyruszenie AirportID i DateTime. Sprzężenie zewnętrzne pozwala nam zachować wszystkie rekordy danych linii lotniczych nawet wtedy, gdy nie ma żadnych pasujących danych pogody. Po przyłączeniu usuniemy pewne nadmiarowe kolumny i zmienimy nazwy przechowywanych kolumn w celu usunięcia prefiksu przychodzącej ramki danych wprowadzonego przez sprzężenie.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

W podobny sposób dołączymy dane pogodowe i lotnicze w oparciu o AirportID przybycia i datę i godzinę:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Zapisz wyniki w formacie CSV dla programu Exchange za pomocą narzędzia skalowanie

Kończy to sprzężenia, które musimy wykonać przy użyciu platformy Spark. Zapisujemy dane z ostatecznej platformy Spark Dataframe "joinedDF5" do pliku CSV, aby uzyskać dane wejściowe do skalowania, a następnie zamknąć sesję platformy Spark. Firma Microsoft jawnie informuje platformę Spark, aby zapisywał wynikowy plik CSV w 80 oddzielnych partycjach w celu zapewnienia wystarczającej równoległości w przetwarzaniu:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importuj do XDF do użycia przez program skaler

W przypadku modelowania za pośrednictwem źródła danych tekstowych skalowania możemy użyć pliku CSV z dołączonymi danymi linii lotniczych i pogody. Jednak najpierw importuje go do XDF, ponieważ jest bardziej wydajny podczas uruchamiania wielu operacji na zestawie danych:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Dzielenie danych do szkolenia i testowania

Korzystamy z rxDataStep, aby podzielić dane 2012 do testowania i zachować resztę do szkoleń:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Uczenie i testowanie modelu regresji logistycznej

Teraz jesteśmy gotowi do skompilowania modelu. Aby zobaczyć wpływ danych pogodowych na opóźnienie w czasie nadejścia, użyjemy procedury regresji logistycznej dla skalowania. Używamy go do modelowania, czy opóźnienie przybycia większe niż 15 minut ma wpływ na Pogoda na lotniskach wyjścia i przybycia:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Teraz zobaczmy, jak to robi na danych testowych, wykonując pewne przewidywania i sprawdzając ROC i AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Ocenianie w innym miejscu

Możemy również użyć modelu do oceniania danych na innej platformie. Zapisanie go do pliku usług pulpitu zdalnego, a następnie przetransferowanie i importowanie tego pulpitu zdalnego do docelowego środowiska oceniania, takiego jak Microsoft SQL Server R Services. Ważne jest, aby zapewnić, że poziomy współczynnika danych mają być oceniane jako zgodne z tymi, na których modelu został skompilowany. Takie dopasowanie można osiągnąć poprzez wyodrębnienie i zapisanie informacji o kolumnach skojarzonych z danymi modelowania za pośrednictwem funkcji skalowania `rxCreateColInfo()` , a następnie zastosowanie informacji o tej kolumnie do źródła danych wejściowych w celu przewidywania. W poniższym przykładzie kodu zapiszemy kilka wierszy zestawu danych testowych i wyodrębnisz i użyjemy informacji o kolumnie z tego przykładu w skrypcie przewidywania:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Podsumowanie

W tym artykule pokazano, jak można połączyć korzystanie z platformy Spark w celu manipulowania danymi za pomocą skalowania w celu rozwoju modelu w usłudze Hadoop Spark. W tym scenariuszu należy zachować oddzielne sesje platformy Spark, uruchamiać tylko jedną sesję i wymieniać dane przy użyciu plików CSV. Chociaż nie jest to proste, ten proces powinien być jeszcze łatwiejszy w przyszłych wydaniach usługi ML, gdy platforma Spark i program do skalowania mogą współużytkować sesję platformy Spark i współużytkować w nim ramki danych Spark.

## <a name="next-steps-and-more-information"></a>Następne kroki i więcej informacji

- Aby uzyskać więcej informacji na temat używania ML Server na Apache Spark, zobacz [Przewodnik wprowadzający](/machine-learning-server/r/how-to-revoscaler-spark).

- Aby uzyskać informacje dotyczące usługi ML w usłudze HDInsight, zobacz [Omówienie usług w usłudze ml w usłudze HDInsight](r-server/r-server-overview.md).

Aby uzyskać więcej informacji na temat korzystania z platformy Spark, zobacz:

- [Dokument platformy Apache Spark](https://spark.apache.org/docs/2.1.0/sparkr.html).

- [Omówienie platformy Spark](/azure/databricks/spark/latest/sparkr/overview)
