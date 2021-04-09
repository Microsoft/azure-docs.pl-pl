---
title: Usuń dane osobowe — serwer proxy aplikacji usługi Azure Active Directory
description: Usuń dane osobowe z łączników zainstalowanych na urządzeniach dla serwer proxy aplikacji usługi Azure Active Directory.
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90913ba8f7fbe8158a5cfea01e49a175180677b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258952"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Usuń dane osobowe serwer proxy aplikacji usługi Azure Active Directory

Serwer proxy aplikacji usługi Azure Active Directory wymaga zainstalowania łączników na urządzeniach, co oznacza, że na urządzeniach mogą znajdować się dane osobowe. W tym artykule przedstawiono procedurę usuwania danych osobowych w celu poprawy prywatności.

## <a name="where-is-the-personal-data"></a>Gdzie są dane osobowe?

Serwer proxy aplikacji może zapisywać dane osobowe w następujących typach dzienników:

- Dzienniki zdarzeń łącznika
- Dzienniki zdarzeń systemu Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Usuń dane osobowe z dzienników zdarzeń systemu Windows

Aby uzyskać informacje na temat konfigurowania przechowywania danych dla dzienników zdarzeń systemu Windows, zobacz [Ustawienia dzienników zdarzeń](https://technet.microsoft.com/library/cc952132.aspx). Aby dowiedzieć się więcej o dziennikach zdarzeń systemu Windows, zobacz [Korzystanie z dziennika zdarzeń systemu Windows](/windows/win32/wes/using-windows-event-log).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Usuń dane osobowe z dzienników zdarzeń łącznika

Aby upewnić się, że Dzienniki serwera proxy aplikacji nie zawierają danych osobowych, możesz wykonać następujące czynności:

- Usuń lub Wyświetl dane, gdy jest to potrzebne, lub
- Wyłącz rejestrowanie

Skorzystaj z poniższych sekcji, aby usunąć dane osobowe z dzienników zdarzeń łącznika. Należy ukończyć proces usuwania dla wszystkich urządzeń, na których zainstalowano łącznik.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Wyświetlanie lub eksportowanie określonych danych

Aby wyświetlić lub wyeksportować określone dane, Wyszukaj powiązane wpisy w każdym z dzienników zdarzeń łącznika. Dzienniki znajdują się w lokalizacji `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` .

Ponieważ dzienniki są plikami tekstowymi, można użyć polecenie [findstr](/windows-server/administration/windows-commands/findstr) do wyszukiwania wpisów tekstowych związanych z użytkownikiem.  

Aby znaleźć dane osobowe, Wyszukaj pliki dzienników dla identyfikatora użytkownika.

Aby znaleźć dane osobowe zarejestrowane przez aplikację, która korzysta z ograniczonego delegowania protokołu Kerberos, Wyszukaj te składniki typu nazwa użytkownika:

- Główna nazwa użytkownika lokalnego
- Część username głównej nazwy użytkownika
- Część username głównej nazwy użytkownika lokalnego
- Nazwa konta lokalnego Menedżera kont zabezpieczeń (SAM)

### <a name="delete-specific-data"></a>Usuń określone dane

Aby usunąć określone dane:

1. Uruchom ponownie usługę łącznika serwera proxy aplikacji Microsoft Azure AD, aby wygenerować nowy plik dziennika. Nowy plik dziennika umożliwia usunięcie lub zmodyfikowanie starych plików dziennika. 
1. Aby znaleźć informacje, które należy usunąć, postępuj zgodnie z opisanym wcześniej w temacie [Przejrzyj lub wyeksportuj określony proces danych](#view-or-export-specific-data) . Przeszukaj wszystkie dzienniki łączników.
1. Usuń odpowiednie pliki dziennika lub selektywnie Usuń pola zawierające dane osobowe. Możesz również usunąć wszystkie stare pliki dziennika, jeśli nie są już potrzebne.

### <a name="turn-off-connector-logs"></a>Wyłączanie dzienników łączników

Jedną z opcji zapewnienia, że dzienniki łączników nie zawierają danych osobowych, jest wyłączenie generowania dziennika. Aby zatrzymać generowanie dzienników łączników, usuń następujący wyróżniony wiersz z `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` .

![Pokazuje fragment kodu z wyróżnionym kodem do usunięcia](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem serwera proxy aplikacji, zobacz [jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](application-proxy.md).