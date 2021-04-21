---
title: Uzyskiwanie dostępu do źródeł danych w środowisku lokalnym
description: Nawiązywanie połączenia z lokalnymi źródłami danych z Azure Logic Apps przez utworzenie zasobu bramy danych na platformie Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 49da5d7f045ed06ba16696ebd16ad212b9d140d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763313"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Connect to on-premises data sources from Azure Logic Apps (Łączenie z lokalnymi źródłami danych z usługi Azure Logic Apps)

Po [zainstalowaniu  ](../logic-apps/logic-apps-gateway-install.md) lokalnej bramy danych na komputerze lokalnym i przed uzyskaniem dostępu do źródeł danych w środowisku lokalnym z aplikacji logiki należy utworzyć zasób bramy na platformie Azure dla instalacji bramy. Następnie możesz wybrać ten zasób bramy w wyzwalaczach i [](../connectors/managed.md#on-premises-connectors) akcjach, których chcesz użyć dla łączników lokalnych dostępnych w Azure Logic Apps. Azure Logic Apps obsługuje operacje odczytu i zapisu za pośrednictwem bramy danych. Jednak te operacje mają [ograniczenia dotyczące rozmiaru ładunku.](/data-integration/gateway/service-gateway-onprem#considerations)

W tym artykule pokazano, jak utworzyć zasób bramy platformy Azure dla wcześniej [zainstalowanej bramy na komputerze lokalnym.](../logic-apps/logic-apps-gateway-install.md) Aby uzyskać więcej informacji na temat bramy, zobacz [Jak działa brama](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Aby bezpośrednio uzyskać dostęp do zasobów lokalnych w sieciach wirtualnych platformy Azure bez konieczności używania bramy, rozważ zamiast tego utworzenie [*środowiska usługi integracji.*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 

Aby uzyskać informacje na temat sposobu używania bramy z innymi usługami, zobacz następujące artykuły:

* [Microsoft Power Automate lokalną bramą danych](/power-automate/gateway-reference)
* [Microsoft Power BI lokalną bramą danych](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps lokalną bramą danych](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

W Azure Logic Apps lokalna brama danych obsługuje łączniki [lokalne](../connectors/managed.md#on-premises-connectors) dla tych źródeł danych:

* BizTalk Server 2016 r.
* System plików
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Baza danych Oracle
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Można również tworzyć [łączniki niestandardowe, które](../logic-apps/custom-connector-overview.md) łączą się ze źródłami danych za pośrednictwem protokołu HTTP lub HTTPS przy użyciu interfejsu REST lub SOAP. Mimo że sama brama nie wiąże się z dodatkowymi kosztami, model [cen](../logic-apps/logic-apps-pricing.md) Logic Apps ma zastosowanie do tych łączników i innych operacji w Azure Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

* Lokalna brama danych [została już zainstalowana na komputerze lokalnym.](../logic-apps/logic-apps-gateway-install.md) Ta instalacja bramy musi istnieć, aby można było utworzyć zasób bramy, który łączy się z tą instalacją.

* Masz to [samo konto i subskrypcję platformy Azure,](../logic-apps/logic-apps-gateway-install.md#requirements) które zostały użyte do instalacji bramy. To konto platformy Azure musi należeć tylko do jednej [dzierżawy Azure Active Directory (Azure AD) lub katalogu](../active-directory/fundamentals/active-directory-whatis.md#terminology). Musisz użyć tego samego konta i subskrypcji platformy Azure, aby utworzyć zasób bramy na platformie Azure, ponieważ tylko administrator bramy może utworzyć zasób bramy na platformie Azure. Jednostki usługi nie są obecnie obsługiwane.

  * Podczas tworzenia zasobu bramy na platformie Azure należy wybrać instalację bramy do połączenia z zasobem bramy i tylko z tym zasobem bramy. Każdy zasób bramy może łączyć się tylko z jedną instalacją bramy. Nie można wybrać instalacji bramy, która została już skojarzona z innym zasobem bramy.

  * Aplikacja logiki i zasób bramy nie muszą istnieć w tej samej subskrypcji platformy Azure. W wyzwalaczach i akcjach, w których można użyć zasobu bramy, możesz wybrać inną subskrypcję platformy Azure, która ma zasób bramy, ale tylko wtedy, gdy ta subskrypcja istnieje w tej samej dzierżawie lub katalogu usługi Azure AD co aplikacja logiki. Musisz również mieć uprawnienia administratora do bramy, które może dla Ciebie skonfigurować inny administrator. Aby uzyskać więcej informacji, zobacz Data [Gateway: Automation using PowerShell - Part 1 and PowerShell:](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) Data Gateway - Add-DataGatewayClusterUser (Brama danych: automatyzacja przy użyciu programu PowerShell — część 1) i [PowerShell: Data Gateway — Add-DataGatewayClusterUser.](/powershell/module/datagateway/add-datagatewayclusteruser)

    > [!NOTE]
    > Obecnie nie można udostępnić zasobu bramy ani instalacji w wielu subskrypcjach. Aby przesłać opinię na temat produktu, [zobacz Microsoft Azure forum opinii.](https://feedback.azure.com/forums/34192--general-feedback)

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Tworzenie zasobu bramy platformy Azure

Po zainstalowaniu bramy na komputerze lokalnym utwórz zasób platformy Azure dla bramy.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) tym samym kontem platformy Azure, które zostało użyte do zainstalowania bramy.

1. W Azure Portal wyszukiwania wpisz "lokalna brama danych", a następnie wybierz **pozycję Lokalne bramy danych.**

   ![Znajdowanie "lokalnej bramy danych"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. W **obszarze Lokalne bramy danych wybierz** pozycję **Dodaj**.

   ![Dodawanie nowego zasobu platformy Azure dla bramy danych](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. W **obszarze Utwórz bramę** połączenia podaj te informacje dotyczące zasobu bramy. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   | Właściwość | Opis |
   |----------|-------------|
   | **Nazwa zasobu** | Podaj nazwę zasobu bramy, która zawiera tylko litery, cyfry, łączniki ( ), podkreślenia `-` ( `_` ), nawiasy ( , ) lub `(` `)` kropki ( `.` ). |
   | **Subskrypcja** | Wybierz subskrypcję platformy Azure dla konta platformy Azure, które zostało użyte do instalacji bramy. Domyślna subskrypcja jest oparta na koncie platformy Azure użytym do zalogowania. |
   | **Grupa zasobów** | Grupa [zasobów platformy Azure,](../azure-resource-manager/management/overview.md) której chcesz użyć |
   | **Lokalizacja** | Ten sam region lub lokalizacja, który został wybrany dla usługi bramy w chmurze podczas [instalacji bramy.](../logic-apps/logic-apps-gateway-install.md) W przeciwnym razie instalacja bramy nie będzie wyświetlana na **liście Nazwa instalacji.** Lokalizacja aplikacji logiki może różnić się od lokalizacji zasobów bramy. |
   | **Nazwa instalacji** | Wybierz instalację bramy, która zostanie wyświetlona na liście tylko wtedy, gdy zostaną spełnione następujące warunki: <p><p>— Instalacja bramy używa tego samego regionu co zasób bramy, który chcesz utworzyć. <br>— Instalacja bramy nie jest połączona z innym zasobem bramy platformy Azure. <br>— Instalacja bramy jest połączona z tym samym kontem platformy Azure, za pomocą których tworzysz zasób bramy. <br>— Twoje konto platformy Azure należy do jednej [dzierżawy lub katalogu Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) i jest tym samym kontem, które było używane podczas instalacji bramy. <p><p>Aby uzyskać więcej informacji, zobacz [sekcję Często zadawane](#faq) pytania. |
   |||

   Oto przykład instalacji bramy, która znajduje się w tym samym regionie co zasób bramy i jest połączona z tym samym kontem platformy Azure:

   ![Podaj szczegółowe informacje dotyczące tworzenia zasobu bramy danych](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Nawiązywanie połączenia z danymi lokalnymi

Po utworzeniu zasobu bramy i skojarzeniu subskrypcji platformy Azure z tym zasobem można teraz utworzyć połączenie między aplikacją logiki i lokalnym źródłem danych przy użyciu bramy.

1. W Azure Portal logiki utwórz lub otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Dodaj łącznik obsługujący połączenia lokalne, na przykład SQL Server **.**

1. Wybierz **pozycję Połącz za pośrednictwem lokalnej bramy danych.**

1. W **obszarze** Brama z **listy Subskrypcja** wybierz subskrypcję platformy Azure, która zawiera zasób bramy, którego potrzebujesz.

   Aplikacja logiki i zasób bramy nie muszą istnieć w tej samej subskrypcji platformy Azure. Możesz wybrać jedną z innych subskrypcji platformy Azure, z których każda ma zasób bramy, ale tylko wtedy, gdy te subskrypcje istnieją w tej samej dzierżawie lub katalogu usługi Azure AD co aplikacja logiki i masz uprawnienia administratora do bramy, które może skonfigurować inny administrator. Aby uzyskać więcej informacji, zobacz Data [Gateway: Automation using PowerShell - Part 1 and PowerShell:](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) Data Gateway - Add-DataGatewayClusterUser (Brama danych: automatyzacja przy użyciu programu PowerShell — część 1) i [PowerShell: Data Gateway — Add-DataGatewayClusterUser.](/powershell/module/datagateway/add-datagatewayclusteruser)
  
1. Z **listy Brama** połączeń, na której są dostępne zasoby bramy w wybranej subskrypcji, wybierz zasób bramy, którego chcesz użyć. Każdy zasób bramy jest połączony z pojedynczą instalacją bramy.

   > [!NOTE]
   > Lista bram zawiera zasoby bramy w innych regionach, ponieważ lokalizacja aplikacji logiki może różnić się od lokalizacji zasobu bramy. 

1. Podaj unikatową nazwę połączenia i inne wymagane informacje, które zależą od połączenia, które chcesz utworzyć.

   Unikatowa nazwa połączenia ułatwia późniejsze znalezienie tego połączenia, szczególnie w przypadku tworzenia wielu połączeń. Jeśli ma to zastosowanie, dołącz również kwalifikowaną domenę dla swojej nazwy użytkownika.

   Oto przykład:

   ![Tworzenie połączenia między aplikacją logiki a bramą danych](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

Połączenie bramy jest teraz gotowe do użycia przez aplikację logiki.

## <a name="edit-connection"></a>Edytowanie połączenia

Aby zaktualizować ustawienia połączenia bramy, możesz edytować połączenie.

1. Aby znaleźć wszystkie połączenia interfejsu API tylko dla Twojej aplikacji logiki, w menu aplikacji logiki w obszarze **Narzędzia** programowe wybierz pozycję **Połączenia interfejsu API.**

   ![W menu aplikacji logiki wybierz pozycję "Połączenia interfejsu API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Wybierz odpowiednie połączenie bramy, a następnie wybierz pozycję **Edytuj połączenie interfejsu API.**

   > [!TIP]
   > Jeśli aktualizacje nie zajdą, spróbuj zatrzymać i ponownie uruchomić konto usługi bramy [systemu Windows](../logic-apps/logic-apps-gateway-install.md#restart-gateway) dla instalacji bramy.

Aby znaleźć wszystkie połączenia interfejsu API skojarzone z subskrypcją platformy Azure:

* Z menu Azure Portal wybierz pozycję **Wszystkie usługi Połączenia internetowego**  >    >  **interfejsu API**.
* Lub z menu Azure Portal wybierz pozycję **Wszystkie zasoby.** Ustaw filtr **Typ na** wartość **Połączenie interfejsu API.**

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Usuwanie zasobu bramy

Aby utworzyć inny zasób bramy, połączyć instalację bramy z innym zasobem bramy lub usunąć zasób bramy, możesz usunąć zasób bramy bez wpływu na instalację bramy.

1. W menu Azure Portal wybierz pozycję **Wszystkie zasoby** lub wyszukaj i wybierz pozycję Wszystkie **zasoby** na dowolnej stronie. Znajdź i wybierz zasób bramy.

1. Jeśli nie została jeszcze wybrana, w menu zasobu bramy wybierz pozycję **Lokalna brama danych.** Na pasku narzędzi zasobu bramy wybierz pozycję **Usuń.**

   Na przykład:

   ![Usuwanie zasobu bramy na platformie Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie:** Dlaczego instalacja bramy nie jest wyświetlana podczas tworzenia zasobu bramy na platformie Azure? <br/>
**A:** Ten problem może wystąpić z tych powodów:

* Konto platformy Azure nie jest tym samym kontem, które było używane do instalacji bramy na komputerze lokalnym. Sprawdź, czy zalogowano się do Azure Portal przy użyciu tej samej tożsamości, która została użyta podczas instalacji bramy. Tylko administrator bramy może utworzyć zasób bramy na platformie Azure. Jednostki usługi nie są obecnie obsługiwane.

* Twoje konto platformy Azure nie należy tylko do jednej dzierżawy lub katalogu [usługi Azure AD.](../active-directory/fundamentals/active-directory-whatis.md#terminology) Sprawdź, czy używasz tej samej dzierżawy lub katalogu usługi Azure AD, która została użyta podczas instalacji bramy.

* Zasób bramy i instalacja bramy nie istnieją w tym samym regionie. Jednak lokalizacja aplikacji logiki może różnić się od lokalizacji zasobu bramy.

* Instalacja bramy jest już skojarzona z innym zasobem bramy. Każdy zasób bramy może łączyć się tylko z jedną instalacją bramy, która może łączyć się tylko z jednym kontem platformy Azure i subskrypcją. Dlatego nie można wybrać instalacji bramy, która została już skojarzona z innym zasobem bramy. Te instalacje nie będą wyświetlane na **liście Nazwa instalacji.**

  Aby przejrzeć rejestracje bramy w usłudze Azure Portal, znajdź wszystkie  zasoby platformy Azure, które  mają typ zasobu Lokalne bramy danych we wszystkich subskrypcjach platformy Azure. Aby odłączyć instalację bramy od innego zasobu bramy, zobacz [Usuwanie zasobu bramy](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie aplikacji logiki](./logic-apps-securing-a-logic-app.md)
* [Typowe przykłady i scenariusze dotyczące aplikacji logiki](./logic-apps-examples-and-scenarios.md)
