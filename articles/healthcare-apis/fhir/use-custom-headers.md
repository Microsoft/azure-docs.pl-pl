---
title: Dodawanie danych do dzienników inspekcji przy użyciu nagłówków niestandardowych — Azure API for FHIR
description: W tym artykule opisano sposób dodawania danych do dzienników inspekcji przy użyciu niestandardowych nagłówków HTTP w interfejsie API platformy Azure dla FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019511"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>Dodawanie danych do dzienników inspekcji przy użyciu niestandardowych nagłówków HTTP

W interfejsie API usługi Azure Fast opieka Resources (FHIR) użytkownik może chcieć uwzględnić dodatkowe informacje w dziennikach, które pochodzą z systemu wywołującego.

Na przykład, gdy użytkownik interfejsu API jest uwierzytelniany przez system zewnętrzny, ten system przekazuje wywołanie do interfejsu API FHIR. W warstwie interfejsu API FHIR informacje o oryginalnym użytkowniku zostały utracone, ponieważ wywołanie zostało przesłane dalej. Może być konieczne zarejestrowanie i zachowanie tych informacji o użytkowniku na potrzeby inspekcji lub zarządzania. System wywołujący może zapewnić tożsamość użytkownika, lokalizację obiektu wywołującego lub inne niezbędne informacje w nagłówkach HTTP, które będą przenoszone wraz z przekazywaniem dalej.

Ten przepływ danych można zobaczyć na poniższym diagramie:

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="Diagram niestandardowych nagłówków":::

Możesz użyć niestandardowych nagłówków do przechwytywania kilku typów informacji. Na przykład:

* Informacje o tożsamości lub autoryzacji
* Pochodzenie obiektu wywołującego
* Organizacja źródłowa
* Szczegóły systemu klienta (elektroniczny rekord kondycji, Portal pacjenta)

> [!IMPORTANT]
> Należy pamiętać, że informacje wysyłane w nagłówkach niestandardowych są przechowywane w wewnętrznym systemie rejestrowania firmy Microsoft przez 30 dni od momentu udostępnienia go w ramach monitorowania dzienników platformy Azure. Zalecamy szyfrowanie wszelkich informacji przed dodaniem ich do niestandardowych nagłówków. Nie należy przekazywać żadnych informacji o usłudze PHI w nagłówkach klientów.

Należy użyć następującej konwencji nazewnictwa dla nagłówków HTTP: X-MS-AZUREFHIR-AUDIT- \<name> .

Te nagłówki HTTP znajdują się w zbiorze właściwości, który jest dodawany do dziennika. Na przykład:

* X-MS-AZUREFHIR-AUDIT-USERID: 1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ: 1234

Te informacje są następnie serializowane do formatu JSON, gdy zostanie on dodany do kolumny właściwości w dzienniku. Na przykład:

```json
{ "X-MS-AZUREFHIR-AUDIT-USERID" : "1234",
"X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "XXXX",
"X-MS-AZUREFHIR-AUDIT-XYZ" : "1234" }
```
 
Podobnie jak w przypadku dowolnego nagłówka HTTP, taka sama nazwa nagłówka może być powtórzona przy użyciu różnych wartości. Na przykład:

* X-MS-AZUREFHIR-AUDIT-USERLOCATION: szpitale
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: nagły

Po dodaniu do dziennika wartości są łączone z listą rozdzielaną przecinkami. Na przykład:

{"X-MS-AZUREFHIR-AUDIT-USERLOCATION": "szpitale, awaryjne"}
 
Można dodać maksymalnie 10 unikatowych nagłówków (powtórzenia tego samego nagłówka z różnymi wartościami są zliczane tylko jako jeden). Łączna Maksymalna długość wartości jednego nagłówka to 2048 znaków.

Jeśli używasz biblioteki interfejsu API klienta Firefly C#, kod wygląda następująco:

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób dodawania danych do dzienników inspekcji przy użyciu niestandardowych nagłówków w interfejsie API platformy Azure dla FHIR. Następnie Dowiedz się więcej na temat innych dodatkowych ustawień, które można skonfigurować w interfejsie API platformy Azure dla usługi FHIR.
 
>[!div class="nextstepaction"]
>[Ustawienia dodatkowe](azure-api-for-fhir-additional-settings.md)
