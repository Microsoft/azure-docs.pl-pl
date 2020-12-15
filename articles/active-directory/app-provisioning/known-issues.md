---
title: Znane problemy związane z obsługą aplikacji w usłudze Azure AD
description: Informacje o znanych problemach podczas pracy z automatyczną obsługą aplikacji w usłudze Azure AD.
author: kenwith
ms.author: kenwith
manager: celestedg
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.reviewer: arvinh
ms.openlocfilehash: 78e9d7355b8c96295456f8aa9cb80b1d3bdd2fab
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509550"
---
# <a name="known-issues-application-provisioning"></a>Znane problemy: Inicjowanie obsługi aplikacji
Znane problemy, które należy wziąć pod uwagę podczas pracy z obsługą aplikacji. Aby przekazać opinię na temat usługi Application Provisioning w witrynie UserVoice, zobacz temat [udostępnianie aplikacji usługi Azure AD](https://aka.ms/appprovisioningfeaturerequest)w usłudze UserVoice. Uważnie obserwujemy usługę UserVoice, abyśmy mogli ulepszyć usługi. 

> [!NOTE]
> Nie jest to kompleksowa lista znanych problemów. Jeśli wiesz, że problem nie znajduje się na liście, Prześlij opinię w dolnej części strony.

## <a name="authorization"></a>Autoryzacja 

**Nie można zapisać po pomyślnym teście połączenia**

Jeśli można pomyślnie przetestować połączenie, ale nie można go zapisać, Przekroczono limit dozwolonych magazynów dla poświadczeń. Aby dowiedzieć się więcej, zobacz [problem z zapisywaniem poświadczeń administratora](./user-provisioning.md).

**Nie można zapisać**

Aby można było zapisać, należy podać adres URL dzierżawy, token tajny i wiadomość e-mail z powiadomieniem. Nie można podać tylko jednego z nich. 

**Nie można zmienić trybu aprowizacji z powrotem na ręczny**

Po skonfigurowaniu aprowizacji po raz pierwszy należy zauważyć, że tryb aprowizacji został przełączony z ręczny na automatyczny. Nie można go zmienić z powrotem na ręczny. Można jednak wyłączyć aprowizacji za pomocą interfejsu użytkownika. Wyłączenie aprowizacji w interfejsie użytkownika efektywnie działa tak samo jak ustawienie listy rozwijanej na ręczne.  


## <a name="attribute-mappings"></a>Mapowania atrybutów 

**Nazwa SamAccountName atrybutu lub UserType nie jest dostępna jako atrybut źródłowy**

Atrybuty SamAccountName i UserType nie są domyślnie dostępne jako atrybut źródłowy. Rozwiń schemat, aby dodać atrybut. Można dodać atrybuty do listy dostępnych atrybutów źródłowych, rozszerzając schemat. Aby dowiedzieć się więcej, zobacz [Brak atrybutu źródłowego](user-provisioning-sync-attributes-for-mapping.md). 

**Brak listy rozwijanej atrybutów źródłowych dla rozszerzenia schematu**

Rozszerzenia do schematu mogą czasami być nieobecne na liście rozwijanej atrybutów źródłowych w interfejsie użytkownika. Przejdź do ustawień zaawansowanych mapowań atrybutów i ręcznie Dodaj atrybuty. Aby dowiedzieć się więcej, zobacz [Dostosowywanie mapowań atrybutów](customize-application-attributes.md).

**Nie można zainicjować obsługi atrybutu null**

Usługa Azure AD obecnie nie może zainicjować obsługi atrybutów o wartości null. Jeśli atrybut ma wartość null w obiekcie użytkownika, zostanie pominięty. 

**Maksymalna liczba znaków w wyrażeniach mapowania atrybutów**

Wyrażenia mapowania atrybutów mogą zawierać maksymalnie 10 000 znaków. 

**Nieobsługiwane filtry określania zakresu**

Rozszerzenia katalogów, appRoleAssignments, UserType i accountExpires nie są obsługiwane jako filtry zakresu.


## <a name="service-issues"></a>Problemy z usługami 

**Nieobsługiwane scenariusze**

- Hasła aprowizacji nie są obsługiwane. 
- Zagnieżdżone grupy nie są obsługiwane. 
- Inicjowanie obsługi dzierżaw B2C nie jest obsługiwane ze względu na rozmiar dzierżawców.
- Nie wszystkie aplikacje aprowizacji są dostępne we wszystkich chmurach. Na przykład Atlassian nie jest jeszcze dostępna w chmurze dla instytucji rządowych. Pracujemy z programistami aplikacji, aby dołączać swoje aplikacje do wszystkich chmur.

**Automatyczna obsługa administracyjna nie jest dostępna w aplikacji opartej na OIDC**

Jeśli utworzysz rejestrację aplikacji, odpowiadająca jej jednostka usługi w aplikacjach dla przedsiębiorstw nie zostanie włączona w celu automatycznego aprowizacji użytkowników. Konieczne będzie zażądanie, aby aplikacja została dodana do galerii, jeśli ma być używana przez wiele organizacji, lub utworzyć drugą aplikację bez galerii do aprowizacji. 

**Interwał aprowizacji został rozwiązany**

[Czas](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) między cyklami aprowizacji nie jest obecnie konfigurowalny. 

**Zmiany, które nie zostały przeniesione z aplikacji docelowej do usługi Azure AD**

Usługa aprowizacji aplikacji nie wie o zmianach wprowadzonych w aplikacjach zewnętrznych. W związku z tym nie jest podejmowana żadna akcja wycofywania. Usługa aprowizacji aplikacji opiera się na zmianach wprowadzonych w usłudze Azure AD. 

**Cykl aprowizacji jest kontynuowany do momentu ukończenia**

Podczas ustawiania aprowizacji `enabled = off` lub przerywania, bieżący cykl aprowizacji będzie kontynuował działanie do momentu ukończenia. Usługa przestanie wykonywać kolejne cykle, dopóki nie zostanie ponownie włączona obsługa administracyjna.

**Członek grupy nie został zainicjowany**

Gdy grupa znajduje się w zakresie, a element członkowski znajduje się poza zakresem, Grupa zostanie zainicjowana. Użytkownik poza zakresem nie zostanie zainicjowany. Jeśli członek powróci do zakresu, usługa nie będzie od razu wykryć zmiany. Ponowne uruchomienie aprowizacji rozwiąże problem. Zalecamy okresowe ponowne uruchamianie usługi, aby upewnić się, że wszyscy użytkownicy mają prawidłowo zainicjowaną obsługę administracyjną.  

**Menedżer nie jest zainicjowany**

Jeśli użytkownik i jego Menedżer są zarówno w zakresie udostępniania, usługa zainicjuje użytkownika, a następnie zaktualizuje Menedżera. Jeśli jednak w dniu, w którym znajduje się użytkownik, a Menedżer znajduje się poza zakresem, udostępnimy użytkownikowi bez odwołania do Menedżera. Gdy Menedżer przyjdzie do zakresu, odwołanie do Menedżera nie zostanie zaktualizowane do momentu ponownego uruchomienia aprowizacji i spowoduje ponowne ocenienie wszystkich użytkowników przez usługę. 

## <a name="next-steps"></a>Następne kroki
- [Jak działa aprowizacja](how-provisioning-works.md)
