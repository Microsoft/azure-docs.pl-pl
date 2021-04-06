---
title: Rozwiązywanie problemów z opóźnieniami przy użyciu dzienników Analityki magazynu
description: Identyfikowanie i rozwiązywanie problemów z opóźnieniami przy użyciu dzienników analitycznych usługi Azure Storage i optymalizowanie aplikacji klienckiej.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 1e6033f9a8f4cecd2429eca67a3d58e54d7ae1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221112"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Rozwiązywanie problemów z opóźnieniami przy użyciu dzienników Analityki magazynu

Diagnozowanie i rozwiązywanie problemów to kluczowa umiejętność tworzenia i obsługi aplikacji klienckich za pomocą usługi Azure Storage.

Ze względu na rozproszony charakter aplikacji platformy Azure, diagnozowanie i rozwiązywanie problemów z błędami i wydajnością może być bardziej skomplikowane niż w tradycyjnych środowiskach.

W poniższych krokach pokazano, jak identyfikować i rozwiązywać problemy z opóźnieniami przy użyciu dzienników analitycznych usługi Azure Storage i zoptymalizować aplikację kliencką.

## <a name="recommended-steps"></a>Zalecane czynności

1. Pobierz [dzienniki analityka magazynu](./manage-storage-analytics-logs.md#download-storage-logging-log-data).

2. Użyj poniższego skryptu programu PowerShell, aby przekonwertować nieprzetworzone dzienniki w formacie tabelarycznym:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Skrypt uruchomi okno graficznego interfejsu użytkownika, w którym można filtrować informacje według kolumn, jak pokazano poniżej.

   ![Okno analizatora dzienników analitycznych magazynu](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Zawęź wpisy dziennika na podstawie "operacji-Type" i poszukaj wpisu dziennika utworzonego w przedziale czasowym problemu.

   ![Wpisy dziennika typu operacji](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. W czasie, gdy wystąpi problem, ważne są następujące wartości:

   * Operation-type = GetBlob
   * żądanie-status = SASNetworkError
   * Kompleksowe-to-end-in-MS = 8453
   * Serwer-opóźnienie-in-MS = 391

   Opóźnienie end-to-end jest obliczane przy użyciu następującego równania:

   * Opóźnienie kompleksowe = Server-Latency + opóźnienie klienta

   Oblicz opóźnienie klienta przy użyciu wpisu dziennika:

   * Opóźnienie klienta = opóźnienie od końca do końca — Server-Latency

        Przykład: 8453 – 391 = 8062ms

   Poniższa tabela zawiera informacje o dużym czasie opóźnienia i stanem żądania wyniki:

   | Typ obiektu BLOB |Stanem żądania =<br>Powodzenie|Stanem żądania =<br>SYGNATUR NetworkError|Zalecenie|
   |---|---|---|---|
   |GetBlob|Tak|Nie|[**Operacja GetBlob:** Stanem żądania = sukces](#getblob-operation-requeststatus--success)|
   |GetBlob|Nie|Tak|[**Operacja GetBlob:** Stanem żądania = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Tak|Nie|[**Operacja Put:** Stanem żądania = sukces](#put-operation-requeststatus--success)|
   |PutBlob|Nie|Tak|[**Operacja Put:** Stanem żądania = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Wyniki stanu

### <a name="getblob-operation-requeststatus--success"></a>Operacja GetBlob: stanem żądania = sukces

Sprawdź następujące wartości, jak wspomniano w kroku 5 sekcji "zalecane kroki":

* Opóźnienie końca do końca
* Server-Latency
* Client-Latency

W **operacji GetBlob** z **stanem żądania = Success**, jeśli **Maksymalny czas** jest poświęcany na **opóźnienia klienta**, oznacza to, że usługa Azure Storage zajmuje dużo czasu na zapisanie danych do klienta. To opóźnienie wskazuje Client-Side problemu.

**Zaleca**

* Zbadaj kod w kliencie.
* Aby zbadać problemy z łącznością sieciową z poziomu klienta, należy użyć programu Wireshark, Microsoft Message Analyzer lub Tcping. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operacja GetBlob: stanem żądania = (SAS) NetworkError

Sprawdź następujące wartości, jak wspomniano w kroku 5 sekcji "zalecane kroki":

* Opóźnienie końca do końca
* Server-Latency
* Client-Latency

W **operacji GetBlob** z **stanem żądania = (SAS) NetworkError**, jeśli **Maksymalny czas** jest poświęcany na **opóźnienia klienta**, najczęściej spotykanym problemem jest odłączenie klienta przed upływem limitu czasu w usłudze Storage.

**Zaleca**

* Zbadaj kod w kliencie, aby zrozumieć, dlaczego i kiedy klient rozłącza się z usługą magazynu.
* Aby zbadać problemy z łącznością sieciową z poziomu klienta, należy użyć programu Wireshark, Microsoft Message Analyzer lub Tcping. 

### <a name="put-operation-requeststatus--success"></a>Operacja Put: stanem żądania = sukces

Sprawdź następujące wartości, jak wspomniano w kroku 5 sekcji "zalecane kroki":

* Opóźnienie końca do końca
* Server-Latency
* Client-Latency

W **operacji Put** z **stanem żądania = Success**, jeśli **Maksymalny czas** jest poświęcany na **opóźnienia klienta**, oznacza to, że klient poświęca więcej czasu na wysyłanie danych do usługi Azure Storage. To opóźnienie wskazuje Client-Side problemu.

**Zaleca**

* Zbadaj kod w kliencie.
* Aby zbadać problemy z łącznością sieciową z poziomu klienta, należy użyć programu Wireshark, Microsoft Message Analyzer lub Tcping. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operacja Put: stanem żądania = (SAS) NetworkError

Sprawdź następujące wartości, jak wspomniano w kroku 5 sekcji "zalecane kroki":

* Opóźnienie końca do końca
* Server-Latency
* Client-Latency

W **operacji PutBlob** z **stanem żądania = (SAS) NetworkError**, jeśli **Maksymalny czas** jest poświęcany na **opóźnienia klienta**, najczęściej spotykanym problemem jest odłączenie klienta przed upływem limitu czasu w usłudze Storage.

**Zaleca**

* Zbadaj kod w kliencie, aby zrozumieć, dlaczego i kiedy klient rozłącza się z usługą magazynu.
* Aby zbadać problemy z łącznością sieciową z poziomu klienta, należy użyć programu Wireshark, Microsoft Message Analyzer lub Tcping.