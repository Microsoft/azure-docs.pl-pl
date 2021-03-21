---
title: Skonfiguruj niestandardową nazwę domeny dla własnej hostowanej bramy usługi Azure API Management | Microsoft Docs
description: W tym temacie opisano procedurę konfigurowania niestandardowej nazwy domeny dla samodzielnej bramy usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: d52bf87b74ae9b1770ed5092738fd05eb9f54fde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491034"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>Konfigurowanie niestandardowej nazwy domeny dla bramy samoobsługowej

W przypadku aprowizacji samodzielnej [bramy usługi Azure API Management](self-hosted-gateway-overview.md)nie jest ona przypisana do nazwy hosta i musi odwoływać się do niej adres IP. W tym artykule przedstawiono sposób mapowania istniejącej niestandardowej nazwy DNS (nazywanej także nazwą hosta) do bramy samoobsługowej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować:

-   Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Wystąpienie API Management. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Brama samodzielna. Aby uzyskać więcej informacji, zobacz [jak zainicjować obsługę bramy samoobsługowej](api-management-howto-provision-self-hosted-gateway.md)
-   Niestandardowa nazwa domeny, do której należy użytkownik lub Twoja organizacja. Ten temat nie zawiera instrukcji dotyczących sposobu pozyskiwania niestandardowej nazwy domeny.
-   Rekord DNS hostowany na serwerze DNS, który mapuje niestandardową nazwę domeny na adres IP bramy samohostowanej. Ten temat nie zawiera instrukcji dotyczących hostowania rekordu DNS.
-   Musisz mieć prawidłowy certyfikat z kluczem publicznym i prywatnym (. PFX). Podmiot lub alternatywna nazwa podmiotu (SAN) musi być zgodna z nazwą domeny (pozwala to API Management wystąpieniu o bezpieczne Uwidacznianie adresów URL za pośrednictwem protokołu TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Dodawanie niestandardowego certyfikatu domeny do usługi API Management

Dodaj niestandardowy certyfikat domeny (. PFX) do wystąpienia API Management lub odwoływania się do certyfikatu przechowywanego w Azure Key Vault. Postępuj zgodnie z instrukcjami w [bezpiecznych usługach zaplecza przy użyciu uwierzytelniania certyfikatu klienta w usłudze Azure API Management](api-management-howto-mutual-certificates.md).

> [!NOTE]
> Zalecamy używanie certyfikatu magazynu kluczy dla domeny bramy samoobsługowej.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Użyj Azure Portal, aby ustawić niestandardową nazwę domeny dla bramy samoobsługowej

1. Wybierz **bramy** z sekcji **wdrażanie i infrastruktura**.
2. Wybierz bramę samoobsługową, dla której chcesz skonfigurować nazwę domeny.
3. Wybierz pozycję **nazwy hostów** w obszarze **Ustawienia**.
4. Wybierz pozycję **+ Dodaj**
5. Wprowadź nazwę zasobu dla nazwy hosta w polu **Nazwa** .
6. Wprowadź nazwę domeny w polu Nazwa **hosta** .
7. Wybierz certyfikat z listy rozwijanej **certyfikatu** .
8. Zaznacz pole wyboru **Negocjuj certyfikat klienta** , jeśli którykolwiek z interfejsów API udostępnianych za pośrednictwem tej bramy używa uwierzytelniania certyfikatu klienta.
    > [!WARNING]
    > To ustawienie jest udostępniane przez wszystkie nazwy domen skonfigurowane dla bramy.
9. Wybierz pozycję **Dodaj** , aby przypisać niestandardową nazwę domeny do wybranej bramy samoobsługowej.

## <a name="next-steps"></a>Następne kroki

[Uaktualnianie i skalowanie usługi](upgrade-and-scale.md)
