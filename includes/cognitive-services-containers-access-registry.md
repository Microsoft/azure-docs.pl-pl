---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67704329"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Uwierzytelnianie prywatnych rejestrów kontenerów za pomocą interfejsu wiersza polecenia platformy Docker

Można uwierzytelniać się za pomocą prywatnego rejestru kontenerów dla kontenerów Cognitive Services na kilka różnych sposobów, ale zalecaną metodą z wiersza polecenia jest użycie [interfejsu CLI platformy Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Użyj [ `docker login` polecenia](https://docs.docker.com/engine/reference/commandline/login/), jak pokazano w poniższym przykładzie, aby zalogować się do programu `containerpreview.azurecr.io` , rejestru kontenerów prywatnych dla kontenerów Cognitive Services. Zastąp *\<username\>* wartość nazwą użytkownika i *\<password\>* hasłem podanym w poświadczeniach uzyskanych z zespołu usługi Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Jeśli poświadczenia zostały zabezpieczone w pliku tekstowym, można połączyć zawartość tego pliku tekstowego za pomocą polecenia, a następnie polecenie, `cat` `docker login` jak pokazano w poniższym przykładzie. Zamień na *\<passwordFile\>* ścieżkę i nazwę pliku tekstowego zawierającego hasło oraz *\<username\>* nazwę użytkownika podaną w poświadczeniach.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
