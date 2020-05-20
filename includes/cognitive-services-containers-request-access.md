---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.openlocfilehash: 66bd78c94e6c54d26959778cc059730c13d02629
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698572"
---
Formularz żąda informacji o użytkowniku, firmie i scenariuszu użytkownika, dla którego będziesz używać kontenera. Po przesłaniu formularza zespół Cognitive Services platformy Azure przegląda go, aby upewnić się, że spełnia on kryteria dostępu do prywatnego rejestru kontenerów.

> [!IMPORTANT]
> Musisz użyć adresu e-mail skojarzonego z kontem Microsoft (MSA) lub kontem Azure Active Directory (Azure AD) w formularzu.

Jeśli Twoje żądanie zostanie zatwierdzone, otrzymasz wiadomość e-mail z instrukcjami opisującymi, jak uzyskać poświadczenia i uzyskać dostęp do prywatnego rejestru kontenerów.

## <a name="log-in-to-the-private-container-registry"></a>Logowanie do rejestru kontenerów prywatnych

Istnieje kilka sposobów uwierzytelniania przy użyciu prywatnego rejestru kontenerów dla kontenerów Cognitive Services. Zalecamy użycie metody wiersza polecenia za pomocą [interfejsu CLI platformy Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Użyj polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/) , jak pokazano w poniższym przykładzie, aby zalogować się do programu `containerpreview.azurecr.io` , który jest rejestrem prywatnych kontenerów dla kontenerów Cognitive Services. Zastąp nazwę * \< username \> * nazwą użytkownika i * \< hasłem \> * przy użyciu hasła podanego w ramach poświadczeń otrzymanych od zespołu usługi Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Jeśli poświadczenia zostały zabezpieczone w pliku tekstowym, można połączyć zawartość tego pliku tekstowego z `docker login` poleceniem. Użyj `cat` polecenia, jak pokazano w poniższym przykładzie. Zastąp * \< passwordFile \> * ścieżką i nazwą pliku tekstowego, który zawiera hasło. Zastąp nazwę * \< username \> * nazwą użytkownika podaną w poświadczeniach.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

