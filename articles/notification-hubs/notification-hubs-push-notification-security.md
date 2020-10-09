---
title: Model zabezpieczeń Notification Hubs
description: Dowiedz się więcej na temat modelu zabezpieczeń dla usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76263765"
---
# <a name="notification-hubs-security"></a>Zabezpieczenia Notification Hubs

## <a name="overview"></a>Omówienie

W tym temacie opisano model zabezpieczeń usługi Azure Notification Hubs.

## <a name="shared-access-signature-security"></a>Zabezpieczenia sygnatury dostępu współdzielonego

Notification Hubs implementuje schemat zabezpieczeń na poziomie jednostki nazywany *sygnaturą dostępu współdzielonego* (SAS). Każda reguła zawiera nazwę, wartość klucza (wspólny klucz tajny) i zestaw praw, jak wyjaśniono w dalszej części [oświadczeń zabezpieczeń](#security-claims). 

Podczas tworzenia centrum automatycznie tworzone są dwie reguły: jeden z prawami do **nasłuchiwania** (używane przez aplikację kliencką) i **jeden z** prawami (których zaplecze aplikacji):

- **DefaultListenSharedAccessSignature**: przyznaje tylko uprawnienia do **nasłuchiwania** .
- **DefaultFullSharedAccessSignature**: przyznaje uprawnienia do **nasłuchiwania**, **zarządzania**i **wysyłania** . Te zasady mają być używane tylko w zasobie zaplecza aplikacji. Nie należy używać go w aplikacjach klienckich; Użyj zasad z dostępem tylko do **nasłuchiwania** . Aby utworzyć nowe zasady dostępu niestandardowego z nowym tokenem SAS, zobacz [tokeny SAS dla zasad dostępu](#sas-tokens-for-access-policies) w dalszej części tego artykułu.

W przypadku zarządzania rejestracją w aplikacjach klienckich, jeśli informacje wysyłane za pośrednictwem powiadomień nie są poufne (na przykład aktualizacje pogody), typowym sposobem uzyskiwania dostępu do centrum powiadomień jest nadanie wartości klucza reguły dostępu tylko do aplikacji klienckiej, a w celu przyznania wartości klucza reguły pełnego dostępu do zaplecza aplikacji.

Aplikacje nie powinny osadzać wartości klucza w aplikacjach klienckich sklepu Windows; Zamiast tego aplikacja kliencka pobiera ją z zaplecza aplikacji podczas uruchamiania.

Klucz z dostępem do **nasłuchiwania** umożliwia aplikacji klienckiej rejestrację w celu dowolnych tagów. Jeśli aplikacja wymaga ograniczenia rejestracji do określonych klientów (na przykład gdy Tagi reprezentują identyfikatory użytkowników), zaplecze aplikacji musi wykonać rejestrację. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md). Należy zauważyć, że w ten sposób aplikacja kliencka nie będzie mieć bezpośredniego dostępu do Notification Hubs.

## <a name="security-claims"></a>Oświadczenia zabezpieczeń

Podobnie jak w przypadku innych jednostek, operacje centrum powiadomień są dozwolone dla trzech oświadczeń zabezpieczeń: **nasłuchiwanie**, **wysyłanie**i **Zarządzanie**.

| Claim   | Opis                                          | Dozwolone operacje |
| ------- | ---------------------------------------------------- | ------------------ |
| Nasłuchiwanie  | Tworzenie/aktualizowanie, odczytywanie i usuwanie pojedynczych rejestracji | Utwórz/zaktualizuj rejestrację<br><br>Odczytaj rejestrację<br><br>Odczytaj wszystkie rejestracje dla dojścia<br><br>Usuń rejestrację |
| Wysyłanie    | Wysyłanie komunikatów do centrum powiadomień                | Wyślij wiadomość |
| Zarządzanie  | CRUDs na Notification Hubs (w tym aktualizowanie poświadczeń PNS i kluczy zabezpieczeń) oraz odczytywanie rejestracji w oparciu o Tagi |Tworzenie/aktualizowanie/odczytywanie/usuwanie centrów<br><br>Odczytaj rejestracje według tagu |

Notification Hubs akceptuje tokeny SYGNATURy dostępu współdzielonego z kluczami udostępnionymi skonfigurowanymi bezpośrednio w centrum.

Wysłanie powiadomienia do więcej niż jednej przestrzeni nazw nie jest możliwe. Przestrzenie nazw są kontenerami logicznymi dla Notification Hubs i nie są związane z wysyłaniem powiadomień.

Użyj zasad dostępu na poziomie przestrzeni nazw (poświadczeń) dla operacji na poziomie przestrzeni nazw; na przykład: Wyświetlanie listy centrów, tworzenie lub usuwanie centrów itp. Tylko zasady dostępu na poziomie centrum umożliwiają wysyłanie powiadomień.

### <a name="sas-tokens-for-access-policies"></a>Tokeny SAS dla zasad dostępu

Aby utworzyć nowe zabezpieczenia lub wyświetlić istniejące klucze SAS, wykonaj następujące czynności:

1. Zaloguj się do Portalu Azure.
2. Wybierz pozycję **Wszystkie zasoby**.
3. Wybierz nazwę centrum powiadomień, dla którego chcesz utworzyć lub wyświetlić klucz sygnatury dostępu współdzielonego.
4. W menu po lewej stronie wybierz pozycję **zasady dostępu**.
5. Wybierz pozycję **nowe zasady** , aby utworzyć nowe zabezpieczenia. Nadaj zasadom nazwę i wybierz uprawnienia, które chcesz udzielić. Następnie wybierz przycisk **OK**.
6. Pełne parametry połączenia (łącznie z nowym kluczem SAS) są wyświetlane w oknie zasady dostępu. Możesz skopiować ten ciąg do Schowka w celu późniejszego użycia.

Aby wyodrębnić klucz sygnatury dostępu współdzielonego z określonych zasad, wybierz przycisk **Kopiuj** obok zasad zawierających odpowiedni klucz SAS. Wklej tę wartość do tymczasowej lokalizacji, a następnie skopiuj część klucza sygnatury dostępu współdzielonego parametrów połączenia. W tym przykładzie używa Notification Hubs przestrzeni nazw o nazwie **mytestnamespace1**i zasad o nazwie **policy2**. Klucz sygnatury dostępu współdzielonego to wartość znajdująca się blisko końca ciągu, określona przez **SharedAccessKey**:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Pobierz klucze SAS](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Następne kroki

- [Przegląd Notification Hubs](notification-hubs-push-notification-overview.md)
