---
title: Rozwiązywanie problemów Azure Data Factory UX
description: Dowiedz się, jak rozwiązywać problemy dotyczące Azure Data Factory środowiska użytkownika.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9f23155df6d9e63448b35974c331bf78c3e5f90c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426230"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Rozwiązywanie problemów dotyczących Azure Data Factory środowiska użytkownika

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów dotyczących środowiska Azure Data Factory.

## <a name="adf-ux-not-loading"></a>Nie załadowanie środowiska ADF

> [!NOTE]
> Azure Data Factory środowisko użytkownika oficjalnie obsługuje przeglądarki Microsoft Edge i Google Chrome. Korzystanie z innych przeglądarek sieci Web może prowadzić do nieoczekiwanego lub nieudokumentowanego zachowania.

### <a name="third-party-cookies-blocked"></a>Zablokowane pliki cookie innych firm

ŚRODOWISKO APD ADF używa plików cookie przeglądarki do utrwalania sesji użytkownika i włączania interaktywnych środowisk programistycznych i monitorowania. Możliwe, że przeglądarka zablokuje pliki cookie innych firm, ponieważ używasz sesji incognito lub włączono blokadę AD. Zablokowanie plików cookie innych firm może powodować problemy podczas ładowania portalu, takie jak przekierowywanie do pustej strony https://adf.azure.com/accesstoken.html lub otrzymywanie komunikatu ostrzegawczego informującego, że pliki cookie innych firm są zablokowane. Aby rozwiązać ten problem, Włącz opcje plików cookie innych firm w przeglądarce, wykonując następujące czynności:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Zezwalaj na wszystkie pliki cookie

1. Odwiedź stronę **Chrome://Settings/cookies** w przeglądarce.
1. Wybierz opcję **Zezwalaj na wszystkie pliki cookie** 

    ![Zezwalaj na wszystkie pliki cookie w przeglądarce Chrome](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Odśwież środowisko ADF i spróbuj ponownie.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Zezwalaj tylko na korzystanie z plików cookie przez środowisko ADF
Jeśli nie chcesz zezwalać na wszystkie pliki cookie, możesz opcjonalnie zezwolić na środowisko ADF:
1. Odwiedź stronę **Chrome://Settings/cookies**.
1. Wybierz pozycję **Dodaj** w obszarze **witryny, które mogą zawsze używać plików cookie** 

    ![Dodawanie środowiska ADF do dozwolonych witryn w programie Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Dodaj witrynę **ADF.Azure.com** , zaznacz opcję **wszystkie pliki cookie** i Zapisz. 

    ![Zezwalaj na wszystkie pliki cookie z witryny środowiska APD usługi ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Odśwież środowisko ADF i spróbuj ponownie.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Odwiedź stronę **Edge://Settings/Content/cookies** w przeglądarce.
1. Upewnij się, że **Lokacje do zapisu i odczytu danych cookie** są włączone i że opcja **Blokuj pliki cookie innych firm** jest wyłączona 

    ![Zezwalaj na wszystkie pliki cookie na krawędzi](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Odśwież środowisko ADF i spróbuj ponownie.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Zezwalaj tylko na korzystanie z plików cookie przez środowisko ADF

Jeśli nie chcesz zezwalać na wszystkie pliki cookie, możesz opcjonalnie zezwolić na środowisko ADF:

1. Odwiedź stronę **Edge://Settings/Content/cookies**.
1. W obszarze **Zezwól** wybierz pozycję **Dodaj** i Dodaj witrynę **ADF.Azure.com** . 

    ![Dodawanie środowiska ADF do dozwolonych witryn w programie Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Odśwież środowisko ADF i spróbuj ponownie.

## <a name="connection-failed-on-adf-ux"></a>Nie można nawiązać połączenia w interfejsie użytkownika usługi ADF

Czasami widzisz błędy "połączenie nie powiodło się" w środowisku APD usługi ADF podobnym do poniższego zrzutu ekranu po kliknięciu przycisku **Testuj połączenie**, **wersji zapoznawczej**itd.

![Nie można nawiązać połączenia](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

W takim przypadku można najpierw spróbować wykonać tę samą operację z trybem przeglądania InPrivate w przeglądarce.

Jeśli nadal nie działa, w przeglądarce naciśnij klawisz F12, aby otworzyć **Narzędzia deweloperskie**. Przejdź do karty **Sieć** , zaznacz opcję **Wyłącz pamięć podręczną**, ponów próbę wykonania operacji zakończonej niepowodzeniem i Znajdź żądanie zakończone niepowodzeniem (w kolorze czerwonym).

![Żądanie zakończone niepowodzeniem](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Następnie Znajdź **nazwę hosta** (w tym przypadku **dpnortheurope.svc.DataFactory.Azure.com**) z **adresu URL żądania** żądania zakończonego niepowodzeniem.

Wpisz **nazwę hosta** bezpośrednio na pasku adresu przeglądarki. Jeśli w przeglądarce widzisz 404, zazwyczaj oznacza to, że po stronie klienta jest to prawidłowe, a problem występuje po stronie usługi ADF. Zastąp bilet pomocy technicznej **identyfikatorem działania** z komunikatu o błędzie środowiska ADF.

![Nie znaleziono zasobu](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

Jeśli nie, lub widzisz podobny błąd poniżej w przeglądarce, zazwyczaj oznacza to, że występuje jakiś problem po stronie klienta. Wykonaj kroki opisane w sekcji Rozwiązywanie problemów.

![Błąd po stronie klienta](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Otwórz **wiersz polecenia** i wpisz **nslookup dpnortheurope.svc.DataFactory.Azure.com**. Normalna odpowiedź powinna wyglądać następująco:

![Odpowiedź na polecenie 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Jeśli widzisz normalną odpowiedź DNS, skontaktuj się z lokalną obsługą IT, aby sprawdzić ustawienia zapory, czy połączenie HTTPS z tą nazwą hosta jest zablokowane. Jeśli problem nie został rozwiązany, należy wysłać bilet pomocy technicznej z **identyfikatorem działania** z komunikatu o błędzie środowiska ADF.

Jeśli zobaczysz coś innego niż to, zazwyczaj oznacza to, że wystąpił problem z serwerem DNS podczas rozpoznawania nazwy DNS. Zwykle może być możliwe obejście tego problemu podczas próby zmiany usługodawcy internetowego (usługodawcy internetowego) lub systemu DNS (np. do usługi Google DNS 8.8.8.8). Jeśli problem będzie się powtarzać, możesz spróbować użyć **polecenia nslookup DataFactory.Azure.com** i **nslookup Azure.com** , aby sprawdzić, na którym poziomie nie powiodło się rozpoznawanie nazw DNS i przesłać wszystkie informacje do lokalnej pomocy technicznej IT lub usługodawcy internetowego w celu rozwiązywania problemów. Jeśli uważasz, że problem nadal występuje po stronie firmy Microsoft, należy wysłać bilet pomocy technicznej z **identyfikatorem działania** z komunikatu o błędzie środowiska ADF.

![Odpowiedź na polecenie 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Zmień typ połączonej usługi w zestawach danych

Zestawu danych formatu pliku można używać ze wszystkimi łącznikami opartymi na plikach, na przykład można skonfigurować zestaw danych Parquet w usłudze Azure Blob lub Azure Data Lake Storage Gen2. Uwaga Każdy Łącznik obsługuje różne ustawienia powiązane z magazynem danych w działaniu i z innym modelem aplikacji. 

W interfejsie użytkownika tworzenia usługi ADF w przypadku korzystania z zestawu danych w ramach działania — łącznie z kopiowaniem, wyszukiwaniem, GetMetadata, usuwaniem działań i w zestawie danych, który ma wskazywać połączoną usługę innego typu od bieżącego (np. przełączenie z systemu plików do ADLS Gen2), zobaczysz następujący komunikat ostrzegawczy. Aby upewnić się, że jest to czysty przełącznik, na podstawie zgody, potoki i działania, które odwołują się do tego zestawu danych zostaną zmodyfikowane w celu użycia nowego typu, a wszystkie istniejące ustawienia magazynu danych, które są niezgodne z nowym typem, zostaną wyczyszczone, ponieważ nie będą już stosowane.

Aby dowiedzieć się więcej na temat obsługiwanych ustawień magazynu danych dla każdego łącznika, można przejść do odpowiedniego artykułu łącznika — > właściwości działania kopiowania, aby wyświetlić listę właściwości szczegółowych. Zapoznaj się z usługą [Amazon S3](connector-amazon-simple-storage-service.md), [obiektem blob platformy Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [File Storage platformy Azure](connector-azure-file-storage.md), [systemem plików](connector-file-system.md), [FTP](connector-ftp.md), [magazynem Google Cloud Storage](connector-google-cloud-storage.md), systemem [HDFS](connector-hdfs.md), [protokołem HTTP](connector-http.md)i [SFTP](connector-sftp.md).

![Komunikat ostrzegawczy](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

* [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
* [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/)
* [Strona pytania&pytań i odpowiedzi](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
