---
title: Dostęp do źródeł danych lokalnych
description: Nawiązywanie połączenia z lokalnymi źródłami danych z Azure Logic Apps przez tworzenie zasobu bramy danych na platformie Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 356e63bb0a749ad0f41d886e75971e9b05c7f9dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99218998"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Connect to on-premises data sources from Azure Logic Apps (Łączenie z lokalnymi źródłami danych z usługi Azure Logic Apps)

Po [zainstalowaniu lokalnej *bramy danych* na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md) i przed uzyskaniem dostępu do źródeł danych lokalnych z aplikacji logiki należy utworzyć zasób bramy na platformie Azure na potrzeby instalacji bramy. Następnie można wybrać ten zasób bramy w wyzwalaczach i akcjach, które mają być używane dla [łączników lokalnych](../connectors/apis-list.md#on-premises-connectors) dostępnych w Azure Logic Apps. Azure Logic Apps obsługuje operacje odczytu i zapisu za pomocą bramy danych. Jednak te operacje mają [limity rozmiaru ładunku](/data-integration/gateway/service-gateway-onprem#considerations).

W tym artykule przedstawiono sposób tworzenia zasobu bramy platformy Azure dla wcześniej [zainstalowanej bramy na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md). Aby uzyskać więcej informacji o bramie, zobacz [jak działa Brama](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Aby bezpośrednio uzyskać dostęp do zasobów lokalnych w sieciach wirtualnych platformy Azure bez konieczności korzystania z bramy, zamiast tego należy rozważyć utworzenie [*środowiska usługi integracji*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Aby uzyskać informacje o sposobach korzystania z bramy z innymi usługami, zobacz następujące artykuły:

* [Microsoft energia — lokalna Brama danych](/power-automate/gateway-reference)
* [Lokalna Brama danych Power BI firmy Microsoft](/power-bi/service-gateway-onprem)
* [Lokalna Brama danych firmy Microsoft dla aplikacji zaawansowanych](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

W Azure Logic Apps lokalna Brama danych obsługuje [Łączniki lokalne](../connectors/apis-list.md#on-premises-connectors) dla tych źródeł danych:

* BizTalk Server 2016
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

Możesz również utworzyć [Łączniki niestandardowe](../logic-apps/custom-connector-overview.md) łączące się ze źródłami danych za pośrednictwem protokołu HTTP lub HTTPS za pomocą interfejsu REST lub protokołu SOAP. Mimo że sama Brama nie wiąże się z dodatkowymi kosztami, [model cenowy Logic Apps](../logic-apps/logic-apps-pricing.md) ma zastosowanie do tych łączników i innych operacji w Azure Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

* [Lokalna Brama danych została już zainstalowana na komputerze lokalnym](../logic-apps/logic-apps-gateway-install.md). Ta instalacja bramy musi istnieć, aby można było utworzyć zasób bramy, który łączy się z tą instalacją.

* Masz to [samo konto platformy Azure i subskrypcję](../logic-apps/logic-apps-gateway-install.md#requirements) , które zostały użyte podczas instalacji bramy. To konto platformy Azure musi należeć tylko do jednej [dzierżawy lub katalogu usługi Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Musisz użyć tego samego konta platformy Azure i subskrypcji, aby utworzyć zasób bramy na platformie Azure, ponieważ tylko administrator bramy może utworzyć zasób bramy na platformie Azure. Nazwy główne usług nie są obecnie obsługiwane.

  * Podczas tworzenia zasobu bramy na platformie Azure należy wybrać instalację bramy, aby połączyć się z zasobem bramy i tylko z tym zasobem bramy. Każdy zasób bramy może łączyć się tylko z jedną instalacją bramy. Nie można wybrać instalacji bramy, która jest już skojarzona z innym zasobem bramy.

  * Twoja aplikacja logiki i zasób bramy nie muszą istnieć w tej samej subskrypcji platformy Azure. W wyzwalaczach i akcjach, w których można korzystać z zasobu bramy, można wybrać inną subskrypcję platformy Azure, która ma zasób bramy, ale tylko wtedy, gdy ta subskrypcja istnieje w tej samej dzierżawie usługi Azure AD lub w katalogu, w którym znajduje się aplikacja logiki. Musisz również mieć uprawnienia administratora do bramy, którą może skonfigurować inny administrator. Aby uzyskać więcej informacji, zobacz [Data Gateway: Automation przy użyciu programu PowerShell — część 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) i [PowerShell: Data Gateway-Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).

    > [!NOTE]
    > Obecnie nie można udostępnić zasobu lub instalacji bramy w wielu subskrypcjach. Aby przesłać opinię na temat produktu, zobacz [Microsoft Azure forum opinii](https://feedback.azure.com/forums/34192--general-feedback).

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Utwórz zasób bramy platformy Azure

Po zainstalowaniu bramy na komputerze lokalnym Utwórz zasób platformy Azure dla bramy.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu tego samego konta platformy Azure, które zostało użyte do zainstalowania bramy.

1. W polu wyszukiwania Azure Portal wprowadź wartość "lokalna Brama danych", a następnie wybierz pozycję **lokalne bramy danych**.

   ![Znajdź "lokalna Brama danych"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. W obszarze **lokalne bramy danych** wybierz pozycję **Dodaj**.

   ![Dodawanie nowego zasobu platformy Azure dla bramy danych](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. W obszarze **Utwórz bramę połączenia** podaj te informacje dla zasobu bramy. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   | Właściwość | Opis |
   |----------|-------------|
   | **Nazwa zasobu** | Podaj nazwę zasobu bramy, która zawiera tylko litery, cyfry, łączniki ( `-` ), znaki podkreślenia ( `_` ), nawiasy ( `(` , `)` ) lub kropki ( `.` ). |
   | **Subskrypcja** | Wybierz subskrypcję platformy Azure dla konta platformy Azure, która była używana na potrzeby instalacji bramy. Domyślna subskrypcja jest oparta na koncie platformy Azure użytym do zalogowania się. |
   | **Grupa zasobów** | [Grupa zasobów platformy Azure](../azure-resource-manager/management/overview.md) , której chcesz użyć |
   | **Lokalizacja** | Ten sam region lub lokalizacja, która została wybrana dla usługi bramy w chmurze podczas [instalacji bramy](../logic-apps/logic-apps-gateway-install.md). W przeciwnym razie instalacja bramy nie zostanie wyświetlona na liście **Nazwa instalacji** . Lokalizacja aplikacji logiki może różnić się od lokalizacji zasobów bramy. |
   | **Nazwa instalacji** | Wybierz instalację bramy, która zostanie wyświetlona na liście tylko wtedy, gdy spełnione są następujące warunki: <p><p>-Instalacja bramy używa tego samego regionu co zasób bramy, który chcesz utworzyć. <br>-Instalacja bramy nie jest połączona z innym zasobem bramy platformy Azure. <br>-Instalacja bramy jest połączona z tym samym kontem platformy Azure, które jest używane do tworzenia zasobu bramy. <br>— Twoje konto platformy Azure należy do pojedynczej [dzierżawy usługi Azure Active Directory (Azure AD) lub katalogu](../active-directory/fundamentals/active-directory-whatis.md#terminology) i jest to samo konto, które było używane podczas instalacji bramy. <p><p>Aby uzyskać więcej informacji, zobacz sekcję [często zadawane pytania](#faq) . |
   |||

   Oto przykład, w którym jest wyświetlana instalacja bramy znajdująca się w tym samym regionie, co zasób bramy i jest połączona z tym samym kontem platformy Azure:

   ![Podaj szczegóły w celu utworzenia zasobu bramy danych](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Nawiązywanie połączenia z danymi lokalnymi

Po utworzeniu zasobu bramy i skojarzeniu subskrypcji platformy Azure z tym zasobem możesz teraz utworzyć połączenie między aplikacją logiki i lokalnym źródłem danych przy użyciu bramy.

1. W Azure Portal Utwórz lub Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Dodaj łącznik obsługujący połączenia lokalne, na przykład **SQL Server**.

1. Wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**.

1. W obszarze **brama**, z listy **subskrypcja** wybierz subskrypcję platformy Azure, która ma żądany zasób bramy.

   Twoja aplikacja logiki i zasób bramy nie muszą istnieć w tej samej subskrypcji platformy Azure. Możesz wybrać jedną z innych subskrypcji platformy Azure, z których każdy ma zasób bramy, ale tylko wtedy, gdy te subskrypcje istnieją w tej samej dzierżawie usługi Azure AD lub w katalogu, w którym znajduje się aplikacja logiki, i masz uprawnienia administratora do bramy, którą może skonfigurować inny administrator. Aby uzyskać więcej informacji, zobacz [Data Gateway: Automation przy użyciu programu PowerShell — część 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) i [PowerShell: Data Gateway-Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).
  
1. Z listy **brama połączeń** , która wyświetla dostępne zasoby bramy w wybranej subskrypcji, wybierz żądany zasób bramy. Każdy zasób bramy jest połączony z instalacją pojedynczej bramy.

   > [!NOTE]
   > Lista bram zawiera zasoby bramy w innych regionach, ponieważ lokalizacja aplikacji logiki może różnić się od lokalizacji zasobów bramy. 

1. Podaj unikatową nazwę połączenia i inne wymagane informacje, które są zależne od połączenia, które chcesz utworzyć.

   Unikatowa nazwa połączenia ułatwia znalezienie tego połączenia później, szczególnie w przypadku tworzenia wielu połączeń. Jeśli ma to zastosowanie, Uwzględnij także kwalifikowaną domenę dla nazwy użytkownika.

   Oto przykład:

   ![Utwórz połączenie między aplikacją logiki a bramą danych](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

Połączenie bramy jest teraz gotowe do użycia przez aplikację logiki.

## <a name="edit-connection"></a>Edytuj połączenie

Aby zaktualizować ustawienia dla połączenia bramy, można edytować połączenie.

1. Aby znaleźć wszystkie połączenia interfejsu API tylko dla aplikacji logiki, w menu aplikacji logiki, w obszarze **Narzędzia programistyczne** wybierz pozycję **połączenia interfejsu API**.

   ![W menu aplikacji logiki wybierz pozycję "połączenia interfejsu API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Wybierz połączenie bramy, którego chcesz użyć, a następnie wybierz pozycję **Edytuj połączenie z interfejsem API**.

   > [!TIP]
   > Jeśli aktualizacje nie zaczną obowiązywać, spróbuj [zatrzymać i ponownie uruchomić konto usługi bramy systemu Windows](../logic-apps/logic-apps-gateway-install.md#restart-gateway) na potrzeby instalacji bramy.

Aby znaleźć wszystkie połączenia interfejsu API skojarzone z subskrypcją platformy Azure:

* Z menu Azure Portal wybierz pozycję **wszystkie usługi**  >    >  **połączenia internetowego interfejsu API**.
* Lub z menu Azure Portal wybierz pozycję **wszystkie zasoby**. Ustaw filtr **typu** dla **połączenia interfejsu API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Usuń zasób bramy

Aby utworzyć inny zasób bramy, Połącz instalację bramy z innym zasobem bramy lub Usuń zasób bramy, a następnie usuń zasób bramy bez wpływu na instalację bramy.

1. Z menu Azure Portal wybierz pozycję **wszystkie zasoby** lub Wyszukaj i wybierz pozycję **wszystkie zasoby** z dowolnej strony. Znajdź i wybierz zasób bramy.

1. Jeśli jeszcze tego nie zrobiono, w menu zasobów bramy wybierz pozycję **lokalna Brama danych**. Na pasku narzędzi zasobów bramy wybierz pozycję **Usuń**.

   Na przykład:

   ![Usuwanie zasobu bramy na platformie Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P**: Dlaczego nie jest wyświetlana żadna instalacja bramy podczas tworzenia zasobu bramy na platformie Azure? <br/>
Odp.: ten problem może wystąpić **z** następujących powodów:

* Twoje konto platformy Azure nie jest tym samym kontem, które zostało użyte do zainstalowania bramy na komputerze lokalnym. Sprawdź, czy zalogowano się do Azure Portal z tą samą tożsamością, która była używana podczas instalacji bramy. Tylko administrator bramy może utworzyć zasób bramy na platformie Azure. Nazwy główne usług nie są obecnie obsługiwane.

* Twoje konto platformy Azure nie należy do pojedynczej [dzierżawy lub katalogu usługi Azure AD](../active-directory/fundamentals/active-directory-whatis.md#terminology). Sprawdź, czy używasz tej samej dzierżawy usługi Azure AD lub katalogu, który został użyty podczas instalacji bramy.

* Instalacja zasobu bramy i bramy nie istnieje w tym samym regionie. Jednak lokalizacja aplikacji logiki może różnić się od lokalizacji zasobów bramy.

* Instalacja bramy jest już skojarzona z innym zasobem bramy. Każdy zasób bramy może łączyć się tylko z jedną instalacją bramy, która może łączyć się tylko z jednym kontem i subskrypcją platformy Azure. Dlatego nie można wybrać instalacji bramy, która jest już skojarzona z innym zasobem bramy. Te instalacje nie będą wyświetlane na liście **Nazwa instalacji** .

  Aby przejrzeć rejestracje bramy w Azure Portal, Znajdź wszystkie zasoby platformy Azure, które mają typ zasobu **lokalne bramy danych** we *wszystkich* subskrypcjach platformy Azure. Aby odłączyć instalację bramy od innego zasobu bramy, zobacz [usuwanie zasobu bramy](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie aplikacji logiki](./logic-apps-securing-a-logic-app.md)
* [Typowe przykłady i scenariusze dotyczące usługi Logic Apps](./logic-apps-examples-and-scenarios.md)
