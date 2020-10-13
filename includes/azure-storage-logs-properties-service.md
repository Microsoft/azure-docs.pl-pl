---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6d90b4b68f1f9f4a0fcd20c1d371a32759449fab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711390"
---
| Właściwość | Opis |
|:--- |:---|
|**accountName** | Nazwa konta magazynu. Przykład: `mystorageaccount`.  |
|**requestUrl** | Żądany adres URL. |
|**userAgentHeader** | Wartość **nagłówka User-Agent** w cudzysłowie. Przykład: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Wartość nagłówka **odwołującego** się. Przykład: `http://contoso.com/about.html`.|
|**Identyfikatorem żądania klienta** | Wartość nagłówka **x-MS-Client-Request-ID** żądania. Przykład: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**element ETag** | Identyfikator ETag dla zwracanego obiektu, w cudzysłowie. Przykład: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Łączny czas, wyrażony w milisekundach, aby wykonać żądaną operację. Ta wartość nie obejmuje opóźnienia sieci (czasu odczytu żądania przychodzącego i wysłania odpowiedzi do zleceniodawcy). Przykład: `22`. |
|**Service** | Usługa skojarzona z tym żądaniem. Na przykład: `blob` , `table` , `files` lub `queue` . |
|**operationCount** | Liczba każdej zarejestrowanej operacji, która jest uwzględniona w żądaniu. Ta liczba zaczyna się od indeksu `0` . Niektóre żądania wymagają więcej niż jednej operacji. Większość żądań wykonuje tylko jedną operację. Przykład: `1`. |
|**requestHeaderSize** | Rozmiar nagłówka żądania wyrażony w bajtach. Przykład: `578`. <br>Jeśli żądanie nie powiedzie się, ta wartość może być pusta. |
|**requestBodySize** | Rozmiar pakietów żądań wyrażony w bajtach, które są odczytywane przez usługę magazynu. <br> Przykład: `0`. <br>Jeśli żądanie nie powiedzie się, ta wartość może być pusta.  |
|**responseHeaderSize** | Rozmiar nagłówka odpowiedzi wyrażony w bajtach. Przykład: `216`. <br>Jeśli żądanie nie powiedzie się, ta wartość może być pusta.  |
|**responseBodySize** | Rozmiar pakietów odpowiedzi zapisanych przez usługę magazynu w bajtach. Jeśli żądanie nie powiedzie się, ta wartość może być pusta. Przykład: `216`.  |
|**requestMd5** | Wartość nagłówka **Content-MD5** lub nagłówka **x-MS-Content-MD5** w żądaniu. Wartość skrótu MD5 określona w tym polu reprezentuje zawartość żądania. Przykład: `788815fd0198be0d275ad329cafd1830`. <br>To pole może być puste.  |
|**serverMd5** | Wartość skrótu MD5 obliczonego przez usługę magazynu. Przykład: `3228b3cf1069a5489b298446321f8521`. <br>To pole może być puste.  |
|**lastModifiedTime** | Czas ostatniej modyfikacji (LMT) dla zwracanego obiektu.  Przykład: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>To pole jest puste dla operacji, które mogą zwracać wiele obiektów. |
|**conditionsUsed** | Rozdzielana średnikami lista par klucz-wartość reprezentujących warunek. Mogą to być następujące warunki: <li> If-Modified — od <li> If-unmodifiedd — od <li> If-Match <li> If-None-Match  <br> Przykład: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Wartość nagłówka Content-Length dla żądania wysłanego do usługi Storage. Jeśli żądanie zakończyło się pomyślnie, ta wartość jest równa requestBodySize. Jeśli żądanie nie powiedzie się, ta wartość nie może być równa requestBodySize lub może być pusta. |
|**tlsVersion** | Wersja protokołu TLS używana w połączeniu żądania. Przykład: `TLS 1.2`. |
|**smbTreeConnectID** | Blok komunikatów serwera (SMB) **treeConnectId** ustanowiony w czasie łączenia drzewa. Na przykład: `0x3` |
|**smbPersistentHandleID** | Identyfikator dojścia trwałego z żądania SMB2 CREATE, które przeżyje połączenia sieciowe.  Przywoływany w [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 jako **SMB2_FILEID. Trwałe**. Na przykład: `0x6003f` |
|**smbVolatileHandleID** | Identyfikator dojścia nietrwałego z żądania SMB2 CREATE, które jest odtwarzane w sieci ponownie nawiązuje połączenie.  Przywoływany w [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 jako **SMB2_FILEID. Nietrwały**. Na przykład: `0xFFFFFFFF00000065` |
|**smbMessageID** | Identyfikator **MessageID**względny połączenia. Na przykład: `0x3b165` |
|**smbCreditsConsumed** | Dane przychodzące lub wychodzące używane przez żądanie w jednostkach 64 KB. Na przykład: `0x3` |
|**smbCommandDetail** | Więcej informacji o tym konkretnym żądaniu, a nie o ogólnym typie żądania. Na przykład: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | Identyfikator **fileid** skojarzony z plikiem lub katalogiem.  W przybliżeniu analogicznie do systemu plików NTFS. Na przykład: `0x9223442405598953` |
|**smbSessionID** | **Identyfikator sesji** SMB2 został utworzony podczas instalacji sesji. Na przykład: `0x8530280128000049` |
|**smbCommandMajor UInt32** | Wartość w **SMB2_HEADER. Command**. Obecnie jest to liczba z przedziału od 0 do 18 włącznie. Na przykład: `0x6` |
|**smbCommandMinor** | Podklasa elementu **SmbCommandMajor**, gdzie to konieczne. Na przykład: `DirectoryCloseAndDelete` |