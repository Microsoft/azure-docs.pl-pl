---
title: Uruchamianie zadań w obszarze konta użytkowników
description: Informacje o typach kont użytkowników i sposobach ich konfigurowania.
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: seodec18
ms.openlocfilehash: b19e0c10834b3c5215d14c6c5ae20caaacb4bc64
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606610"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Uruchamianie zadań w obszarze konta użytkowników w usłudze Batch

> [!NOTE]
> Konta użytkowników omówione w tym artykule różnią się od kont użytkowników używanych dla Remote Desktop Protocol (RDP) lub Secure Shell (SSH) ze względów bezpieczeństwa.
>
> Aby nawiązać połączenie z węzłem z uruchomioną konfiguracją maszyny wirtualnej z systemem Linux za pośrednictwem protokołu SSH, zobacz [Install and configure xrdp to pulpit zdalny use with Ubuntu](../virtual-machines/linux/use-remote-desktop.md). Aby nawiązać połączenie z węzłami z systemem Windows za pośrednictwem protokołu RDP, zobacz [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](../virtual-machines/windows/connect-logon.md).
>
> Aby nawiązać połączenie z węzłem z uruchomioną konfiguracją usługi w chmurze za pośrednictwem protokołu RDP, zobacz [włączanie Podłączanie pulpitu zdalnego dla roli w usłudze Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).

Zadanie w Azure Batch zawsze działa w ramach konta użytkownika. Domyślnie zadania są uruchamiane w ramach kont użytkowników standardowych, bez uprawnień administratora. W przypadku niektórych scenariuszy można skonfigurować konto użytkownika, w ramach którego ma zostać uruchomione zadanie. W tym artykule omówiono typy kont użytkowników i sposób ich konfigurowania dla danego scenariusza.

## <a name="types-of-user-accounts"></a>Typy kont użytkowników

Azure Batch oferuje dwa typy kont użytkowników do uruchamiania zadań:

- **Konta użytkowników.** Konta użytkowników wbudowanych to wbudowane konta użytkowników, które są tworzone automatycznie przez usługę Batch. Domyślnie zadania są uruchamiane w ramach konta autoużytkownika. Można skonfigurować specyfikację autoużytkownika dla zadania, aby wskazać, pod którym kontem automatycznie należy uruchomić zadanie. Specyfikacja autoużytkownika umożliwia określenie poziomu podniesienia uprawnień i zakresu konta użytkownika, które będzie uruchamiać zadanie.

- **Nazwane konto użytkownika.** Podczas tworzenia puli można określić jedno lub więcej nazwanych kont użytkowników dla puli. Każde konto użytkownika jest tworzone w każdym węźle puli. Oprócz nazwy konta należy określić hasło konta użytkownika, poziom podniesienia uprawnień oraz, w przypadku pul systemu Linux, klucz prywatny SSH. Po dodaniu zadania można określić nazwane konto użytkownika, w ramach którego ma zostać uruchomione zadanie.

> [!IMPORTANT]
> Usługa Batch w wersji 2017 -01-01.4.0 wprowadziła istotną zmianę, która wymaga zaktualizowania kodu w celu wywołania tej wersji lub nowszej. Zobacz [Aktualizowanie kodu do najnowszej biblioteki klienta usługi Batch,](#update-your-code-to-the-latest-batch-client-library) Aby uzyskać szybkie wskazówki dotyczące aktualizowania kodu partii ze starszej wersji.

## <a name="user-account-access-to-files-and-directories"></a>Dostęp do plików i katalogów przez konto użytkownika

Zarówno konto użytkownika, jak i konto użytkownika nazwanego mają dostęp do odczytu i zapisu do katalogu roboczego zadania, katalogu udostępnionego i zadania o wiele wystąpień. Oba typy kont mają dostęp do odczytu do katalogów uruchamiania i przygotowania zadań.

Jeśli zadanie zostanie uruchomione w ramach tego samego konta, które zostało użyte do uruchomienia zadania podrzędnego, zadanie ma dostęp do odczytu i zapisu do katalogu zadań początkowych. Podobnie, jeśli zadanie zostanie uruchomione w ramach tego samego konta, które zostało użyte do uruchomienia zadania przygotowania zadania, zadanie ma dostęp do odczytu i zapisu do katalogu zadania przygotowania zadania. Jeśli zadanie zostanie uruchomione przy użyciu innego konta niż zadanie uruchom zadanie podrzędne lub przygotowywanie zadania, to zadanie ma dostęp tylko do odczytu do odpowiedniego katalogu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do plików i katalogów z zadania, zobacz [pliki i katalogi](files-and-directories.md).

## <a name="elevated-access-for-tasks"></a>Podwyższony poziom dostępu do zadań

Poziom podniesienia uprawnień konta użytkownika wskazuje, czy zadanie jest uruchamiane z podniesionymi uprawnieniami dostępu. Zarówno konto użytkownika, jak i nazwane konto użytkownika mogą działać z podwyższonym dostępem. Dostępne są dwie opcje poziomu podniesienia uprawnień:

- **Nieadministracyjne:** Zadanie jest uruchamiane jako użytkownik standardowy bez podwyższonego poziomu dostępu. Domyślny poziom podniesienia uprawnień dla konta użytkownika usługi Batch jest zawsze **nieadministracyjny**.
- **Administrator:** Zadanie jest uruchamiane jako użytkownik z podwyższonym poziomem dostępu i działa z pełnymi uprawnieniami administratora.

## <a name="auto-user-accounts"></a>Konta użytkowników

Domyślnie zadania są uruchamiane w usłudze Batch w ramach konta użytkownika standardowego, jako użytkownik standardowy bez podwyższonego poziomu uprawnień i z zakresem puli. Zakres puli oznacza, że zadanie jest uruchamiane w ramach konta użytkownika, które jest dostępne dla każdego zadania w puli. Aby uzyskać więcej informacji na temat zakresu puli, zobacz [Uruchamianie zadania jako użytkownika z zakresem puli](#run-a-task-as-an-auto-user-with-pool-scope).

Alternatywą dla zakresu puli jest zakres zadań. Po skonfigurowaniu dla zakresu zadania specyfikacji dla autoużytkownika usługa Batch tworzy konto użytkownika tylko dla tego zadania.

Istnieją cztery możliwe konfiguracje dla specyfikacji autoużytkownika, z których każdy odnosi się do unikatowego konta użytkownika:

- Dostęp niebędący administratorem z zakresem zadania
- Dostęp administratora (podwyższony poziom) do zakresu zadania
- Dostęp niebędący administratorem z zakresem puli
- Dostęp administratora z zakresem puli

> [!IMPORTANT]
> Zadania uruchomione w ramach zakresu zadania nie mają dostępu do innych zadań w węźle. Jednak złośliwy użytkownik mający dostęp do konta może obejść to ograniczenie, przesyłając zadanie, które jest uruchamiane z uprawnieniami administratora i uzyskuje dostęp do innych katalogów zadań. Złośliwy użytkownik może również nawiązać połączenie z węzłem za pomocą protokołu RDP lub SSH. Ważne jest, aby chronić dostęp do kluczy konta w usłudze Batch w celu uniknięcia takiego scenariusza. Jeśli podejrzewasz, że Twoje konto mogło zostać naruszone, należy ponownie wygenerować klucze.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Uruchamianie zadania jako użytkownik mający podwyższony poziom dostępu

Można skonfigurować specyfikację autoużytkownika dla uprawnień administratora, gdy konieczne jest uruchomienie zadania z podwyższonym poziomem uprawnień. Na przykład zadanie uruchamiania może potrzebować podwyższonego poziomu dostępu w celu zainstalowania oprogramowania w węźle.

> [!NOTE]
> Używaj podwyższonego poziomu dostępu tylko w razie potrzeby. Najlepsze rozwiązania zalecają przyznanie minimalnego uprawnienia niezbędnego do osiągnięcia żądanego wyniku. Na przykład, jeśli zadanie startowe instaluje oprogramowanie dla bieżącego użytkownika, a nie dla wszystkich użytkowników, może być możliwe uniknięcie przyznawania podwyższonego poziomu dostępu do zadań. Można skonfigurować specyfikację autoużytkownika dla zakresu puli i dostęp niebędący administratorami dla wszystkich zadań, które muszą być uruchamiane w ramach tego samego konta, w tym zadania podrzędnego uruchamiania.

Poniższe fragmenty kodu pokazują, jak skonfigurować specyfikację autoużytkownika. Przykłady ustawiają poziom podniesienia uprawnień `Admin` i zakres do `Task` .

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```

#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Uruchamianie zadania jako użytkownika z zakresem puli

Po przydzieleniu węzła w każdym węźle w puli są tworzone dwa konta użytkowników z podwyższonym poziomem uprawnień oraz jeden bez podwyższonego poziomu dostępu. Ustawienie zakresu autoużytkownika do zakresu puli dla danego zadania uruchamia zadanie w ramach jednego z tych dwóch kont użytkowników w całej puli.

Po określeniu zakresu puli dla autoużytkownika wszystkie zadania uruchamiane z uprawnieniami administratora są uruchamiane w ramach tego samego konta autoużytkownika w całej puli. Podobnie zadania, które są uruchamiane bez uprawnień administratora, również są uruchamiane w ramach pojedynczego konta użytkownika w całej puli.

> [!NOTE]
> Dwa konta użytkowników w całej puli są oddzielnymi kontami. Zadania uruchomione w ramach konta administratora na całej puli nie mogą udostępniać danych za pomocą zadań uruchomionych w ramach konta standardowego i na odwrót.

Korzystanie z tego samego konta użytkownika jest możliwe, ponieważ zadania mogą współużytkować dane z innymi zadaniami uruchomionymi w tym samym węźle.

Udostępnianie kluczy tajnych między zadaniami odbywa się w jednym scenariuszu, w którym uruchomione są zadania podrzędne na jednym z dwóch kont użytkowników, które są używane w całej puli. Załóżmy na przykład, że zadanie uruchamiania musi zainicjować obsługę klucza tajnego w węźle, który może być używany przez inne zadania. Można użyć interfejsu API ochrony danych systemu Windows (DPAPI), ale wymaga uprawnień administratora. Zamiast tego można chronić klucz tajny na poziomie użytkownika. Zadania uruchomione w ramach tego samego konta użytkownika mogą uzyskiwać dostęp do klucza tajnego bez podwyższonego poziomu dostępu.

Innym scenariuszem, w którym można uruchamiać zadania w ramach konta autoużytkownika z zakresem puli jest udział plików interfejsu przekazywania komunikatów (MPI). Udział plików MPI jest przydatny, gdy węzły w zadaniu MPI muszą korzystać z tych samych danych plików. Węzeł główny tworzy udział plików, do którego węzły podrzędne mogą uzyskać dostęp, jeśli są uruchomione w ramach tego samego konta użytkownika.

Poniższy fragment kodu ustawia zakres autoużytkownika do puli dla zadania w usłudze Batch .NET. Poziom podniesienia uprawnień jest pomijany, więc zadanie jest uruchamiane w ramach standardowego konta użytkownika w całej puli.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Nazwy kont użytkowników

Można zdefiniować nazwanych kont użytkowników podczas tworzenia puli. Nazwane konto użytkownika ma podaną nazwę i hasło. Możesz określić poziom podniesienia uprawnień dla nazwanego konta użytkownika. W przypadku węzłów systemu Linux można także podać klucz prywatny SSH.

Konto użytkownika o nazwie istnieje na wszystkich węzłach w puli i jest dostępne dla wszystkich zadań uruchomionych w tych węzłach. Dla puli można zdefiniować dowolną liczbę użytkowników z nazwami. Po dodaniu kolekcji zadań lub zadań można określić, że zadanie jest uruchamiane w ramach jednego z nazwanych kont użytkowników zdefiniowanych w puli.

Konto użytkownika nazwanego jest przydatne, gdy chcesz uruchamiać wszystkie zadania w ramach zadania w ramach tego samego konta użytkownika, ale Izoluj je od zadań uruchomionych w innych zadaniach w tym samym czasie. Można na przykład utworzyć nazwanego użytkownika dla każdego zadania i uruchomić zadania poszczególnych zadań w ramach tego konta użytkownika. Każde zadanie może następnie udostępnić tajne zadania, ale nie z zadaniami uruchomionymi w innych zadaniach.

Można również użyć nazwanego konta użytkownika do uruchomienia zadania, które ustawia uprawnienia do zasobów zewnętrznych, takich jak udziały plików. Przy użyciu nazwanego konta użytkownika kontrolujesz tożsamość użytkownika i można użyć tej tożsamości użytkownika do ustawiania uprawnień.  

Konta użytkowników nazwanych umożliwiają włączenie protokołu SSH bez hasła między węzłami systemu Linux. Można użyć nazwanego konta użytkownika z węzłami systemu Linux, które wymagają uruchamiania zadań obejmujących wiele wystąpień. Każdy węzeł w puli może uruchamiać zadania w ramach konta użytkownika zdefiniowanego w całej puli. Aby uzyskać więcej informacji na temat zadań z wieloma wystąpieniami, zobacz [Używanie \- zadań z wieloma wystąpieniami do uruchamiania aplikacji MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Tworzenie nazwanych kont użytkowników

Aby utworzyć konto nazwanych użytkowników w usłudze Batch, Dodaj kolekcję kont użytkowników do puli. Poniższe fragmenty kodu przedstawiają sposób tworzenia nazwanych kont użytkowników w językach .NET, Java i Python. Te fragmenty kodu przedstawiają sposób tworzenia kont administratora i niebędących administratorami nazwanymi w puli. Przykłady tworzą pule przy użyciu konfiguracji usługi w chmurze, ale podczas tworzenia puli systemu Windows lub Linux przy użyciu konfiguracji maszyny wirtualnej należy użyć tej samej metody.

#### <a name="batch-net-example-windows"></a>Przykład platformy .NET w usłudze Batch (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Przykład platformy .NET w usłudze Batch (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "Standard_A1",
    virtualMachineConfiguration: virtualMachineConfiguration);
// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-java-example"></a>Przykład dla języka Java w usłudze Batch

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Przykład kodu Python w usłudze Batch

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Uruchamianie zadania przy użyciu konta użytkownika o podniesionych uprawnieniach

Aby uruchomić zadanie jako podwyższony użytkownik, należy ustawić właściwość **tożsamość użytkownika** zadania na nazwę konta użytkownika, które zostało utworzone za pomocą właściwości **ElevationLevel** ustawionej na `Admin` .

Ten fragment kodu określa, że zadanie powinno być uruchamiane przy użyciu konta użytkownika o nazwie. To nazwane konto użytkownika zostało zdefiniowane w puli podczas tworzenia puli. W takim przypadku nazwane konto użytkownika zostało utworzone z uprawnieniami administratora:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Aktualizowanie kodu do najnowszej biblioteki klienta w usłudze Batch

Usługa Batch w wersji 2017 -01-01.4.0 wprowadziła istotną zmianę, zastępując Właściwość **runElevated** dostępną we wcześniejszych wersjach właściwością **tożsamość użytkownika** . W poniższych tabelach przedstawiono proste mapowanie, których można użyć do zaktualizowania kodu z wcześniejszych wersji bibliotek klienckich.

### <a name="batch-net"></a>Batch .NET

| Jeśli kod używa...                  | Aktualizuj do...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` nie określono | Aktualizacja nie jest wymagana                                                                                               |

### <a name="batch-java"></a>Batch Java

| Jeśli kod używa...                      | Aktualizuj do...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` nie określono | Aktualizacja nie jest wymagana                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Jeśli kod używa...                      | Aktualizuj do...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` nie określono | Aktualizacja nie jest wymagana                                                                                                                                  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Informacje o [plikach i katalogach](files-and-directories.md) w Azure Batch.
