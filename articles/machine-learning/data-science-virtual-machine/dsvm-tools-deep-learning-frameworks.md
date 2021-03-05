---
title: Głębokie uczenie & platformy AI
titleSuffix: Azure Data Science Virtual Machine
description: Dostępne platformy i narzędzia uczenia głębokiego na platformie Azure Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 0bfdea6c0bbbe40e4c72ab9da70a1b16d4bc4d3a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175672"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Głębokie uczenie i platformy AI dla Data Science VM platformy Azure
Poniżej wymieniono platformy uczenia głębokiego na DSVM.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | |
| Obsługiwane wersje DSVM      | Ubuntu 16.04    |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Caffe jest zainstalowany w `/opt/caffe` .   Przykłady znajdują się w `/opt/caffe/examples` .|
| Jak uruchomić go      | Użyj X2Go, aby zalogować się do maszyny wirtualnej, a następnie uruchomić nowy terminal, a następnie wprowadź następujące polecenie:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Zostanie otwarte nowe okno przeglądarki z przykładowymi notesami. Pliki binarne są instalowane w/opt/Caffe/Build/Install/bin.<br/><br/>Zainstalowana wersja programu Caffe wymaga języka Python 2,7 i nie będzie współdziałać z językiem Python 3,5, który jest domyślnie aktywowany. Aby przełączyć się do języka Python 2,7, uruchom polecenie, `source activate root` Aby przełączyć się do środowiska Anaconda.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | |
| Obsługiwane wersje DSVM      | Ubuntu 16.04     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Caffe2 jest zainstalowany w środowisku [Python 2,7 (root) Conda. |
| Jak uruchomić go      | Terminal: uruchamianie języka Python i importowanie Caffe2. <br/> * JupyterHub: [Połącz się z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie przejdź do katalogu Caffe2, aby znaleźć przykładowe notesy. Niektóre notesy wymagają ustawienia elementu głównego Caffe2 w kodzie języka Python; Wprowadź/opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 5.2 |
| Obsługiwane wersje DSVM      | Ubuntu 16.04    |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Moduł łańcucha jest instalowany w języku Python 3,5. |
| Jak uruchomić go      | Terminal: Aktywuj środowisko Python 3,5, uruchom `python` polecenie, a następnie polecenie `import chainer` . <br/> * JupyterHub: [Połącz się z JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie przejdź do katalogu łańcucha, aby znaleźć przykładowe notesy.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[Sterownik CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 10.0.130|
| Obsługiwane wersje DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> System Windows 2016<br> Ubuntu 16.04  |
| Jak jest on skonfigurowany/instalowany w DSVM?  |_NVIDIA-SMI_ jest dostępny w ścieżce systemowej.  |
| Jak uruchomić go      | Otwórz wiersz polecenia (w systemie Windows) lub terminal (na komputerze z systemem Linux), a następnie uruchom polecenie _NVIDIA-SMI_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 0.16.1|
| Obsługiwane wersje DSVM      | Ubuntu 18.04<br> Ubuntu 16.04   |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Horovod jest zainstalowany w języku Python 3,5 |
| Jak uruchomić go      | Aktywuj poprawne środowisko w terminalu, a następnie uruchom Język Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 2.2.4 |
| Obsługiwane wersje DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> System Windows 2016 <br> Ubuntu 16.04   |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Keras jest zainstalowany w języku Python 3,6 w systemie Windows i w języku Python 3,5 w Linux |
| Jak uruchomić go      | Aktywuj poprawne środowisko w terminalu, a następnie uruchom Język Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](/cognitive-toolkit/)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 2.5.1 |
| Obsługiwane wersje DSVM      | System Windows 2016 <br> Ubuntu 16.04   |
| Jak jest on skonfigurowany/instalowany w DSVM?  | CNTK jest zainstalowany w języku Python 3,6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) i w języku Python 3,5 w systemie [Linux](./dsvm-tools-languages.md#python-linux-edition) |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko i uruchom Język Python. <br/>Jupyter: Połącz się z [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog CNTK dla przykładów. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 1.3.0 |
| Obsługiwane wersje DSVM      | System Windows 2016 <br> Ubuntu 16.04    |
| Jak jest on skonfigurowany/instalowany w DSVM?  | MXNet jest zainstalowany w systemie `C:\dsvm\tools\mxnet` Windows i `/dsvm/tools/mxnet` na Ubuntu. Powiązania języka Python są instalowane w języku Python 3,6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) i w języku Python 3,5 on [Linux](./dsvm-tools-languages.md#python-linux-edition)) powiązania R są również zawarte w Ubuntu DSVM. |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko Conda, a następnie uruchom polecenie `import mxnet` . <br/>Jupyter: Połącz się z [Jupyter](provision-vm.md#access-the-dsvm) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz `mxnet` katalog dla przykładów. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 1.0.1 |
| Obsługiwane wersje DSVM      | System Windows 2016 <br> Ubuntu 16.04    |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Serwer modelu MXNet jest zainstalowany w języku Python 3,6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) i w języku Python 3,5 w systemie [Linux](./dsvm-tools-languages.md#python-linux-edition). |
| Jak uruchomić go      | Terminal: Uruchom `sudo systemctl stop jupyterhub` , aby zatrzymać usługę JupyterHub, ponieważ obie nasłuchują na tym samym porcie. Następnie aktywuj poprawne środowisko Conda i uruchom polecenie `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[Interfejs zarządzania systemem NVidia (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje |  |
| Obsługiwane wersje DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> System Windows 2016<br> Ubuntu 16.04   |
| Co to jest? | Narzędzie NVIDIA do wykonywania zapytań dotyczących aktywności procesora GPU |
| Jak jest on skonfigurowany/instalowany w DSVM?  | `nvidia-smi` znajduje się na ścieżce systemowej. |
| Jak uruchomić go      | Na maszynie wirtualnej **z procesorem GPU** Otwórz wiersz polecenia (w systemie Windows) lub terminal (na komputerze z systemem Linux), a następnie uruchom polecenie `nvidia-smi` . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 1.2.0 (Ubuntu 16,04), 1.7.1 (Ubuntu 18,04, Windows 2019) |
| Obsługiwane wersje DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Zainstalowane w języku [Python 3,7](dsvm-tools-languages.md#python-linux-edition) py37_pytorch środowisku Conda. Przykładowe notesy Jupyter są dołączone, a przykłady znajdują się w/dsvm/Samples/pytorch. |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko, a następnie uruchom Język Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Połącz, a następnie otwórz katalog PyTorch dla przykładów.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 1.13 |
| Obsługiwane wersje DSVM      | Windows Server 2019<br>Ubuntu 18.04<br> System Windows 2016 <br> Ubuntu 16.04 |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Zainstalowane w języku Python 3,5 w systemach [Linux](dsvm-tools-languages.md#python-linux-edition) i Python 3,6 w [systemie Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Jak uruchomić go      | Terminal: Aktywuj poprawne środowisko, a następnie uruchom Język Python. <br/> * Jupyter: Połącz się z [Jupyter](provision-vm.md) lub [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), a następnie otwórz katalog TensorFlow dla przykładów.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 1.12 |
| Obsługiwane wersje DSVM      | Ubuntu 16.04 |
| Jak jest on skonfigurowany/instalowany w DSVM?  | tensorflow_model_server jest dostępny w terminalu. |
| Jak uruchomić go      |  Przykłady są dostępne w [trybie online](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| Kategoria | Wartość |
| ------------- | ------------- |
| Obsługiwane wersje | 1.0.3 |
| Obsługiwane wersje DSVM      | Ubuntu 16.04 |
| Jak jest on skonfigurowany/instalowany w DSVM?  |Theano jest zainstalowany w języku Python 2,7 (_root_) i w środowisku Python 3,5 (_py35_). |
| Jak uruchomić go      |  Terminal: Aktywuj żądaną wersję języka Python (root lub py35), uruchom środowisko Python, a następnie zaimportuj Theano.<br/>* Jupyter: Wybierz jądro Python 2,7 lub 3,5, a następnie zaimportuj Theano.  <br/>Aby obejść ostatnią usterkę biblioteki jądra matematycznego (MKL), musisz najpierw ustawić warstwę wątku MKL w następujący sposób:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
