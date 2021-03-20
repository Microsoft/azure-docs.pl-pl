---
title: Uruchamianie zadań U-SQL w języku Python, R i C# — Azure Data Lake Analytics
description: Dowiedz się, jak za pomocą kodu w języku Python, R i C# przesłać zadanie w Azure Data Lake.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/22/2017
ms.custom: devx-track-python
ms.openlocfilehash: d6066bd6ec2a4c986ae17ad0cce3e7f6f73b21e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219976"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Opracowywanie języka U-SQL przy użyciu języków Python, R i C# dla Azure Data Lake Analytics w Visual Studio Code
Dowiedz się, jak za pomocą Visual Studio Code (programu vscode) napisać kod w języku Python, R i C# za pomocą języka U-SQL i przesłać zadania do usługi Azure Data Lake. Aby uzyskać więcej informacji o Azure Data Lake narzędziach programu vscode, zobacz [Korzystanie z kodu Azure Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-for-vscode.md).

Przed zapisaniem niestandardowego kodu związanego z kodem należy otworzyć folder lub obszar roboczy w programu vscode.


## <a name="prerequisites-for-python-and-r"></a>Wymagania wstępne dotyczące języków Python i R
Zarejestruj zestawy rozszerzeń języka Python i rozszerzenia R dla konta usługi ADL. 
1. Otwórz swoje konto w portalu.
   - Wybierz pozycję **Omówienie**. 
   - Kliknij pozycję **przykładowy skrypt**.
2. Kliknij pozycję **Więcej**.
3. Wybierz pozycję **Zainstaluj rozszerzenia U-SQL**. 
4. Komunikat z potwierdzeniem jest wyświetlany po zainstalowaniu rozszerzeń U-SQL. 

   ![Konfigurowanie środowiska dla języków Python i R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Aby uzyskać najlepsze środowisko w języku Python i usłudze języka R, zainstaluj rozszerzenie programu vscode Python i R. 

## <a name="develop-python-file"></a>Opracowywanie pliku języka Python
1. Kliknij **nowy plik** w obszarze roboczym.
2. Napisz swój kod w języku U-SQL. Poniżej znajduje się przykładowy kod.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Kliknij prawym przyciskiem myszy plik skryptu, a następnie wybierz pozycję **ADL: Generuj plik z kodem** w języku Python. 
4. Plik **xxx.usql.py** jest generowany w folderze roboczym. Napisz swój kod w pliku języka Python. Poniżej znajduje się przykładowy kod.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Kliknij prawym przyciskiem myszy plik **USQL** , a następnie kliknij polecenie **Kompiluj skrypt** lub **Prześlij zadanie** , aby uruchomić zadanie.

## <a name="develop-r-file"></a>Tworzenie pliku języka R
1. Kliknij **nowy plik** w obszarze roboczym.
2. Napisz swój kod w pliku U-SQL. Poniżej znajduje się przykładowy kod.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Kliknij prawym przyciskiem myszy plik **USQL** , a następnie wybierz pozycję **ADL: Generate The R Code File**. 
4. Plik **xxx. usql. r** jest generowany w folderze roboczym. Napisz kod w pliku R. Poniżej znajduje się przykładowy kod.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Kliknij prawym przyciskiem myszy plik **USQL** , a następnie kliknij polecenie **Kompiluj skrypt** lub **Prześlij zadanie** , aby uruchomić zadanie.

## <a name="develop-c-file"></a>Opracowywanie pliku C#
Plik związany z kodem to plik języka C# skojarzony z jednym skryptem U-SQL. Można zdefiniować skrypt dedykowany dla UDO, UDA, UDT i UDF w pliku związanym z kodem. UDO, UDA, UDT i UDF można używać bezpośrednio w skrypcie bez wcześniejszego rejestrowania zestawu. Plik związany z kodem jest umieszczany w tym samym folderze co plik skryptu U-SQL. Jeśli skrypt ma nazwę xxx. usql, kod źródłowy jest nazwany jako xxx. usql. cs. Jeśli ręcznie usuniesz plik związany z kodem, funkcja związana z kodem zostanie wyłączona dla skojarzonego skryptu U-SQL. Aby uzyskać więcej informacji na temat pisania kodu klienta dla skryptu U-SQL, zobacz [pisanie i używanie kodu niestandardowego w języku u-SQL: funkcje User-Defined]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Kliknij **nowy plik** w obszarze roboczym.
2. Napisz swój kod w pliku U-SQL. Poniżej znajduje się przykładowy kod.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Kliknij prawym przyciskiem myszy plik **USQL** , a następnie wybierz pozycję **ADL: Generuj kod CS związany z plikiem**. 
4. Plik **xxx. usql. cs** jest generowany w folderze roboczym. Napisz swój kod w pliku CS. Poniżej znajduje się przykładowy kod.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Kliknij prawym przyciskiem myszy plik **USQL** , a następnie kliknij polecenie **Kompiluj skrypt** lub **Prześlij zadanie** , aby uruchomić zadanie.

## <a name="next-steps"></a>Następne kroki
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Lokalne uruchamianie skryptu U-SQL i debugowanie lokalne przy użyciu Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Wprowadzenie do Data Lake Analytics przy użyciu programu PowerShell](data-lake-analytics-get-started-powershell.md)
* [Wprowadzenie do Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-get-started-portal.md)
* [Używanie narzędzi Data Lake Tools for Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Użyj wykazu Data Lake Analytics (U-SQL)](./data-lake-analytics-u-sql-get-started.md)