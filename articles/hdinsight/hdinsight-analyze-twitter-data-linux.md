---
title: Analizowanie danych z usługi Twitter za pomocą Apache Hive — Azure HDInsight
description: Dowiedz się, jak używać Apache Hive i Apache Hadoop w usłudze HDInsight do przekształcania nieprzetworzonych danych usługi TWitter w tabelę programu Hive z możliwością wyszukiwania.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: 8031e917d998b877e6c3a5830d69abf81c9bdebe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086725"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analizowanie danych usługi Twitter przy użyciu Apache Hive i Apache Hadoop w usłudze HDInsight

Dowiedz się, jak za pomocą usługi [Apache Hive](https://hive.apache.org/) przetwarzać dane w usłudze Twitter. Wynikiem jest lista użytkowników usługi Twitter, którzy otrzymali najwięcej tweetów zawierających określone słowo.

> [!IMPORTANT]  
> Kroki opisane w tym dokumencie zostały przetestowane w usłudze HDInsight 3,6.

## <a name="get-the-data"></a>Pobieranie danych

Serwis Twitter umożliwia pobieranie danych dla każdego tweetu jako dokumentu JavaScript Object Notation (JSON) za pomocą interfejsu API REST. Uwierzytelnianie [OAuth](https://oauth.net) jest wymagane na potrzeby uwierzytelniania w interfejsie API.

### <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

1. W przeglądarce internetowej Zaloguj się do [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/) . Wybierz link **Utwórz konto teraz** , jeśli nie masz konta w usłudze Twitter.

2. Wybierz pozycję **Utwórz nową aplikację**.

3. Wprowadź **nazwę**, **Opis**, **witrynę sieci Web**. Możesz wprowadzić adres URL dla pola **Witryna sieci Web** . W poniższej tabeli przedstawiono niektóre przykładowe wartości do użycia:

   | Pole | Wartość |
   |--- |--- |
   | Nazwa |MyHDInsightApp |
   | Opis |MyHDInsightApp |
   | Witryna internetowa |`https://www.myhdinsightapp.com` |

4. Wybierz pozycję **tak, zgadzam**się, a następnie wybierz pozycję **Utwórz aplikację w usłudze Twitter**.

5. Wybierz kartę **uprawnienia** . Uprawnienie domyślne jest **tylko do odczytu**.

6. Wybierz kartę **klucze i tokeny dostępu** .

7. Wybierz pozycję **Utwórz mój token dostępu**.

8. Wybierz pozycję **Testuj OAuth** w prawym górnym rogu strony.

9. Zapisz **klucz klienta**, wpis **tajny klienta**, **token dostępu**i **klucz tajny tokenu dostępu**.

### <a name="download-tweets"></a>Pobierz tweety

Poniższy kod języka Python pobiera 10 000 tweetów z serwisu Twitter i zapisuje je w pliku o nazwie **tweets.txt**.

> [!NOTE]  
> Poniższe kroki są wykonywane w klastrze usługi HDInsight, ponieważ język Python jest już zainstalowany.

1. Użyj [polecenia SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Użyj następujących poleceń, aby zainstalować [Tweepy](https://www.tweepy.org/), [pasek postępu](https://pypi.python.org/pypi/progressbar/2.2)i inne wymagane pakiety:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

1. Edytuj Poniższy kod, zastępując `Your consumer secret` , `Your consumer key` , `Your access token` i `Your access token secret` z odpowiednimi informacjami z aplikacji usługi Twitter. Następnie wklej edytowany kod jako zawartość pliku **gettweets.py** .

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > Dostosuj filtr tematów w ostatnim wierszu, aby śledzić Popularne słowa kluczowe. Przy użyciu słów kluczowych popularnych w momencie uruchomienia skryptu można szybciej przechwytywać dane.

1. Użyj **kombinacji klawiszy Ctrl + X**, a następnie **Y** , aby zapisać plik.

1. Użyj następującego polecenia, aby uruchomić plik i pobrać tweety:

    ```bash
    python gettweets.py
    ```

    Zostanie wyświetlony wskaźnik postępu. Liczba pobieranych tweetów jest równa 100%.

   > [!NOTE]  
   > Jeśli na pasku postępu trwa długi czas, należy zmienić filtr w celu śledzenia tematów trendów. Jeśli masz wiele tweetów dotyczących tematu w filtrze, możesz szybko uzyskać potrzebne tweety 100.

### <a name="upload-the-data"></a>Przekaż dane

Aby przekazać dane do magazynu usługi HDInsight, użyj następujących poleceń:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Te polecenia przechowują dane w lokalizacji, do której mogą uzyskać dostęp wszystkie węzły w klastrze.

## <a name="run-the-hiveql-job"></a>Uruchamianie zadania HiveQL

1. Użyj następującego polecenia, aby utworzyć plik zawierający instrukcje [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) :

   ```bash
   nano twitter.hql
   ```

    Użyj następującego tekstu jako zawartości pliku:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. Naciśnij **klawisze Ctrl + X**, a następnie naciśnij klawisz **t** , aby zapisać plik.

1. Użyj następującego polecenia, aby uruchomić HiveQL zawarty w pliku:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    To polecenie uruchamia plik **Twitter. HQL** . Po zakończeniu zapytania zostanie wyświetlony `jdbc:hive2//localhost:10001/>` monit.

1. W wierszu polecenia z usługi Beeline Użyj następującego zapytania, aby sprawdzić, czy dane zostały zaimportowane:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    To zapytanie zwraca maksymalnie 10 tweetów, które zawierają wyraz " **Azure** " w wiadomości tekstowej.

    > [!NOTE]  
    > Jeśli filtr został zmieniony w `gettweets.py` skrypcie, Zamień **platformę Azure** na jeden z użytych filtrów.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak przekształcić zestaw danych JSON bez struktury do tabeli strukturalnej [Apache Hive](https://hive.apache.org/) . Aby dowiedzieć się więcej na temat usługi Hive w usłudze HDInsight, zobacz następujące dokumenty:

* [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu usługi HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
