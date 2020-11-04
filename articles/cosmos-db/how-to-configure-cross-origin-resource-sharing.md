---
title: Współużytkowanie zasobów między źródłami (CORS) w Azure Cosmos DB
description: W tym artykule opisano sposób konfigurowania współużytkowania zasobów między źródłami (CORS) w Azure Cosmos DB przy użyciu szablonów Azure Portal i Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: eba49ff45ba9ab1f5cfaa1d75973d656ac32ca6a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339905"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Skonfiguruj współużytkowanie zasobów między źródłami (CORS)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Współużytkowanie zasobów między źródłami (CORS) to funkcja protokołu HTTP, która umożliwia aplikacji sieci Web działającej w ramach jednej domeny dostęp do zasobów w innej domenie. Przeglądarki sieci Web implementują ograniczenie zabezpieczeń nazywane zasadami tego samego źródła, które uniemożliwiają stronie sieci Web wywoływanie interfejsów API w innej domenie. Jednak mechanizm CORS zapewnia bezpieczny sposób, aby umożliwić domenie pochodzenia wywoływanie interfejsów API w innej domenie. Interfejs API Core (SQL) w Azure Cosmos DB obsługuje teraz funkcję współużytkowania zasobów między źródłami (CORS) przy użyciu nagłówka "allowedOrigins". Po włączeniu obsługi mechanizmu CORS dla konta usługi Azure Cosmos są oceniane tylko uwierzytelnione żądania, aby określić, czy są dozwolone zgodnie z określonymi regułami.

Ustawienia udostępniania zasobów między źródłami (CORS) można skonfigurować z poziomu Azure Portal lub szablonu Azure Resource Manager. W przypadku kont usługi Cosmos przy użyciu interfejsu API Core (SQL) Azure Cosmos DB obsługuje bibliotekę języka JavaScript działającą w środowiskach Node.js i opartych na przeglądarce. Ta biblioteka może teraz korzystać z obsługi mechanizmu CORS w przypadku korzystania z trybu bramy. Do korzystania z tej funkcji nie jest wymagana konfiguracja po stronie klienta. W przypadku obsługi mechanizmu CORS zasoby z przeglądarki mogą bezpośrednio uzyskać dostęp do Azure Cosmos DB za pośrednictwem [biblioteki JavaScript](https://www.npmjs.com/package/@azure/cosmos) lub bezpośrednio z [interfejsu API REST](/rest/api/cosmos-db/) w przypadku prostych operacji.

> [!NOTE]
> Obsługa mechanizmu CORS jest odpowiednia i obsługiwana dla interfejsu API Azure Cosmos DB Core (SQL). Nie ma zastosowania do Azure Cosmos DB interfejsów API dla Cassandra, Gremlin lub MongoDB, ponieważ te protokoły nie używają protokołu HTTP do komunikacji klient-serwer.

## <a name="enable-cors-support-from-azure-portal"></a>Włączanie obsługi mechanizmu CORS z poziomu Azure Portal

Wykonaj następujące kroki, aby włączyć współużytkowanie zasobów między źródłami przy użyciu Azure Portal:

1. Przejdź do konta usługi Azure Cosmos DB. Otwórz blok **CORS** .

2. Określ rozdzieloną przecinkami listę źródeł, które mogą nawiązywać wywołania między źródłami do konta Azure Cosmos DB. Na przykład, `https://www.mydomain.com` , `https://mydomain.com` `https://api.mydomain.com` . Możesz również użyć symbolu wieloznacznego " \* ", aby zezwolić na wszystkie źródła i wybrać opcję **Prześlij**. 

   > [!NOTE]
   > Obecnie nie można używać symboli wieloznacznych jako części nazwy domeny. Na przykład `https://*.mydomain.net` format nie jest jeszcze obsługiwany. 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="Włącz udostępnianie zasobów między źródłami przy użyciu Azure Portal":::

## <a name="enable-cors-support-from-resource-manager-template"></a>Włączanie obsługi mechanizmu CORS przy użyciu szablonu Menedżer zasobów

Aby włączyć mechanizm CORS przy użyciu szablonu Menedżer zasobów, należy dodać sekcję "CORS" z właściwością "allowedOrigins" do dowolnego istniejącego szablonu. Poniższy kod JSON jest przykładem szablonu, który tworzy nowe konto usługi Azure Cosmos z włączonym mechanizmem CORS.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Korzystanie z Azure Cosmos DB biblioteki JavaScript z przeglądarki

Obecnie Biblioteka Azure Cosmos DB JavaScript ma tylko wersję CommonJS biblioteki, która została dostarczona z jej pakietem. Aby użyć tej biblioteki z przeglądarki, należy użyć narzędzia, takiego jak ROLLUP lub WebPack, aby utworzyć bibliotekę zgodną z przeglądarką. Niektóre biblioteki Node.js powinny mieć dla nich makiety przeglądarki. Poniżej znajduje się przykładowy plik konfiguracji pakietu WebPack z niezbędnymi ustawieniami makiety.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Oto [przykład kodu](https://github.com/christopheranderson/cosmos-browser-sample) , który używa języka TypeScript i WebPack z biblioteką SDK Azure Cosmos DB JavaScript do tworzenia aplikacji do zrobienia, która wysyła aktualizacje w czasie rzeczywistym po utworzeniu nowych elementów.
Najlepszym rozwiązaniem jest nieużywanie klucza podstawowego do komunikowania się z Azure Cosmos DB z przeglądarki. Zamiast tego należy użyć tokenów zasobów do komunikacji. Aby uzyskać więcej informacji na temat tokenów zasobów, zobacz [Zabezpieczanie dostępu do Azure Cosmos DB](secure-access-to-data.md#resource-tokens) artykułu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innych sposobów zabezpieczania konta usługi Azure Cosmos, zobacz następujące artykuły:

* [Skonfiguruj zaporę dla Azure Cosmos DB](how-to-configure-firewall.md) artykułu.

* [Konfigurowanie sieci wirtualnej i dostępu opartego na podsieci dla konta usługi Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
