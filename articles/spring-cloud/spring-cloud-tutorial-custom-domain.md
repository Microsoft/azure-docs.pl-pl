---
title: Samouczek — mapowanie istniejącej domeny niestandardowej na chmurę z platformą Azure
description: Jak zmapować istniejącą nazwę niestandardowego usługi nazw rozproszonych (DNS) na chmurę Azure
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: 5b57a2463815d5db1c83d3bc4e8cd56314fb204d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176994"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapowanie istniejącej domeny niestandardowej na chmurę z platformą Azure
Usługa nazw rozproszonych (DNS) to technika przechowywania nazw węzłów sieciowych w całej sieci. Ten samouczek mapuje domenę, taką jak www.contoso.com, przy użyciu rekordu CNAME. Zabezpiecza domenę niestandardową za pomocą certyfikatu i pokazuje, jak wymusić Transport Layer Security (TLS), znane także jako SSL (SSL). 

Certyfikaty szyfrują ruch internetowy. Te certyfikaty TLS/SSL mogą być przechowywane w Azure Key Vault. 

## <a name="prerequisites"></a>Wymagania wstępne
* Aplikacja wdrożona w chmurze Azure wiosennej (zobacz [Szybki Start: uruchamianie istniejącej aplikacji w chmurze platformy Azure przy użyciu Azure Portal](spring-cloud-quickstart-launch-app-portal.md)lub użycie istniejącej aplikacji).
* Nazwa domeny z dostępem do rejestru DNS dla dostawcy domeny, na przykład GoDaddy.
* Prywatny certyfikat od innego dostawcy. Certyfikat musi być zgodny z domeną.
* Wdrożone wystąpienie [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Importowanie certyfikatu 
Procedura importowania certyfikatu wymaga, aby plik w formacie PEM lub PFX znajdował się na dysku, a użytkownik musi mieć klucz prywatny. 

Aby przekazać certyfikat do magazynu kluczy:
1. Przejdź do wystąpienia magazynu kluczy.
1. W okienku nawigacji po lewej stronie kliknij pozycję **Certyfikaty**.
1. W górnym menu kliknij pozycję **Generuj/Importuj**.
1. W oknie dialogowym **Tworzenie certyfikatu** w obszarze **metoda tworzenia certyfikatu**wybierz opcję `Import`.
1. W obszarze **Przekaż plik certyfikatu**przejdź do lokalizacji certyfikatu i wybierz ją.
1. W obszarze **hasło**wprowadź klucz prywatny certyfikatu.
1. Kliknij przycisk **Utwórz**.

![Importuj certyfikat 1](./media/custom-dns-tutorial/import-certificate-a.png)

Aby zaimportować certyfikat do chmury wiosennej platformy Azure:
1. Przejdź do wystąpienia usługi. 
1. W okienku nawigacji po lewej stronie aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL**.
1. Następnie kliknij pozycję **Importuj certyfikat Key Vault**.

![Importowanie certyfikatu](./media/custom-dns-tutorial/import-certificate.png)

Po pomyślnym zaimportowaniu certyfikatu zostanie on wyświetlony na liście **certyfikatów kluczy prywatnych**.

![Certyfikat klucza prywatnego](./media/custom-dns-tutorial/key-certificates.png)

>[!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal musisz powiązać certyfikat z określoną domeną. Wykonaj kroki opisane w tym dokumencie pod nagłówkiem **Dodaj powiązanie SSL**.

## <a name="add-custom-domain"></a>Dodaj domenę niestandardową
Możesz użyć rekordu CNAME, aby zmapować niestandardową nazwę DNS na chmurę wiosenną platformy Azure. 

> [!NOTE] 
> Rekord A nie jest obsługiwany. 

### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME
Przejdź do dostawcy DNS i Dodaj rekord CNAME, aby zmapować domenę na <service_name>. azuremicroservices.io. W tym miejscu <service_name> jest nazwą Twojego wystąpienia chmury Azure wiosennej. Obsługujemy wieloznaczną domenę i domenę podrzędną. Po dodaniu rekordu CNAME Strona rekordów DNS będzie wyglądać podobnie do poniższego przykładu: 

![Strona rekordów DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapowanie domeny niestandardowej do aplikacji w chmurze platformy Azure
Jeśli nie masz aplikacji w chmurze Azure wiosennej, postępuj zgodnie z instrukcjami w [przewodniku szybki start: uruchamianie istniejącej aplikacji w chmurze platformy Azure przy użyciu Azure Portal](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Przejdź do strony aplikacji.

1. Wybierz opcję **domena niestandardowa**.
2. Następnie **Dodaj domenę niestandardową**. 

![Domena niestandardowa](./media/custom-dns-tutorial/custom-domain.png)

3. Wpisz w pełni kwalifikowaną nazwę domeny, do której dodano rekord CNAME, taki jak www.contoso.com. Upewnij się, że typ rekordu nazwy hosta jest ustawiony na wartość CNAME (<service_name>. azuremicroservices.io)
4. Kliknij przycisk **Sprawdź poprawność** , aby włączyć przycisk **Dodaj** .
5. Kliknij pozycję **Add** (Dodaj).

![Dodaj domenę niestandardową](./media/custom-dns-tutorial/add-custom-domain.png)

Jedna aplikacja może mieć wiele domen, ale jedna domena może mapować tylko jedną aplikację. Po pomyślnym zmapowaniu domeny niestandardowej do aplikacji zobaczysz ją w tabeli domeny niestandardowej.

![Tabela domen niestandardowych](./media/custom-dns-tutorial/custom-domain-table.png)

>[!NOTE]
> Etykieta **niezabezpieczona** dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem SSL. Wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie.

## <a name="add-ssl-binding"></a>Dodawanie powiązania SSL
W tabeli domena niestandardowa wybierz pozycję **Dodaj powiązanie SSL** , jak pokazano na poprzedniej ilustracji.  
1. Wybierz **certyfikat** lub zaimportuj go.
1. Kliknij przycisk **Zapisz**.

![Dodawanie powiązania SSL](./media/custom-dns-tutorial/add-ssl-binding.png)

Po pomyślnym dodaniu powiązania SSL stan domeny będzie bezpieczny: **dobra kondycja**. 

![Dodawanie powiązania SSL](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Wymuszanie protokołu HTTPS
Domyślnie każdy może nadal uzyskiwać dostęp do aplikacji przy użyciu protokołu HTTP, ale można przekierować wszystkie żądania HTTP do portu HTTPS.

Na stronie aplikacji w lewym okienku nawigacji wybierz pozycję **domena niestandardowa**. Następnie ustaw **tylko https**na *wartość true*.

![Dodawanie powiązania SSL](./media/custom-dns-tutorial/enforce-http.png)

Po zakończeniu operacji przejdź do dowolnego adresu URL HTTPS, który wskazuje aplikację. Należy zauważyć, że adresy URL protokołu HTTP nie działają.

## <a name="see-also"></a>Zobacz też
* [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importowanie certyfikatu](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Uruchamianie aplikacji w chmurze wiosny przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

