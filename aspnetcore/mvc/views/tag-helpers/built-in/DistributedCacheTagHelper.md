---
title: Distributed auxiliar de marca de Cache | Microsoft Docs
author: pkellner
description: Mostra como trabalhar com o auxiliar de marca de Cache
keywords: ASP.NET Core, o auxiliar de marca
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a022
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper
ms.openlocfilehash: b6e0beca0833b1dbe0843e8f8848b976726cc7b0
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2017
---
# <a name="distributed-cache-tag-helper"></a><span data-ttu-id="59a0f-104">Auxiliar de marca de Cache distribuído</span><span class="sxs-lookup"><span data-stu-id="59a0f-104">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="59a0f-105">Por [Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="59a0f-105">By [Peter Kellner](http://peterkellner.net)</span></span> 


<span data-ttu-id="59a0f-106">O auxiliar de marca de Cache distribuído fornece a capacidade de melhorar drasticamente o desempenho do seu aplicativo ASP.NET Core armazenando em cache o conteúdo a uma fonte de cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="59a0f-106">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="59a0f-107">O auxiliar de marca de Cache distribuído herda da classe base mesmo como o auxiliar de marca de Cache.</span><span class="sxs-lookup"><span data-stu-id="59a0f-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span>  <span data-ttu-id="59a0f-108">Todos os atributos associados com o auxiliar de marca de Cache também funcionará no auxiliar de marca distribuídas.</span><span class="sxs-lookup"><span data-stu-id="59a0f-108">All attributes associated with the Cache Tag Helper will also work on the Distributed Tag Helper.</span></span>


<span data-ttu-id="59a0f-109">O auxiliar de marca de Cache distribuído segue o **princípio de dependências explícitas** conhecido como **injeção de construtor**.</span><span class="sxs-lookup"><span data-stu-id="59a0f-109">The Distributed Cache Tag Helper follows the **Explicit Dependencies Principle** known as **Constructor Injection**.</span></span>  <span data-ttu-id="59a0f-110">Especificamente, o `IDistributedCache` contêiner de interface é passado para o construtor de Distributed Cache marca do auxiliar.</span><span class="sxs-lookup"><span data-stu-id="59a0f-110">Specifically, the `IDistributedCache` interface container is passed into the Distributed Cache Tag Helper's constructor.</span></span>  <span data-ttu-id="59a0f-111">Se nenhuma implementação concreta específica de `IDistributedCache` foi criado no `ConfigureServices`, geralmente encontrado em startup.cs, em seguida, o auxiliar de marca de Cache distribuído usará o mesmo provedor de memória para armazenar dados em cache como o auxiliar de marca de Cache basic.</span><span class="sxs-lookup"><span data-stu-id="59a0f-111">If no specific concrete implementation of `IDistributedCache` has been created in `ConfigureServices`, usually found in startup.cs, then the Distributed Cache Tag Helper will use the same in-memory provider for storing cached data as the basic Cache Tag Helper.</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="59a0f-112">Distributed atributos de auxiliar de marca de Cache</span><span class="sxs-lookup"><span data-stu-id="59a0f-112">Distributed Cache Tag Helper Attributes</span></span>

- - -

### <a name="enabled-expires-on-expires-after-expires-sliding-vary-by-header-vary-by-query-vary-by-route-vary-by-cookie-vary-by-user-vary-by-priority"></a><span data-ttu-id="59a0f-113">habilitado expira em expira após expirar deslizantes variar por cabeçalho variam por consulta variam por rota variam por cookie variam por usuário variam por prioridade</span><span class="sxs-lookup"><span data-stu-id="59a0f-113">enabled expires-on expires-after expires-sliding vary-by-header vary-by-query vary-by-route vary-by-cookie vary-by-user vary-by priority</span></span>

<span data-ttu-id="59a0f-114">Consulte auxiliar de marca de Cache de definições.</span><span class="sxs-lookup"><span data-stu-id="59a0f-114">See Cache Tag Helper for definitions.</span></span> <span data-ttu-id="59a0f-115">Auxiliar de marca de Cache distribuído herda a mesma classe auxiliar de marca de Cache para que todos esses atributos são comuns do auxiliar de marca de Cache.</span><span class="sxs-lookup"><span data-stu-id="59a0f-115">Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper so all these attributes are common from Cache Tag Helper.</span></span>

- - -

### <a name="name-required"></a><span data-ttu-id="59a0f-116">nome (obrigatório)</span><span class="sxs-lookup"><span data-stu-id="59a0f-116">name (required)</span></span>

| <span data-ttu-id="59a0f-117">Tipo de atributo</span><span class="sxs-lookup"><span data-stu-id="59a0f-117">Attribute Type</span></span>    | <span data-ttu-id="59a0f-118">Valor de exemplo</span><span class="sxs-lookup"><span data-stu-id="59a0f-118">Example Value</span></span>     |
|----------------   |----------------   |
| <span data-ttu-id="59a0f-119">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="59a0f-119">string</span></span>    | <span data-ttu-id="59a0f-120">"my-distributed-cache-unique-key-101"</span><span class="sxs-lookup"><span data-stu-id="59a0f-120">"my-distributed-cache-unique-key-101"</span></span>     |

<span data-ttu-id="59a0f-121">Obrigatório `name` atributo é usado como uma chave de cache armazenado para cada instância de um auxiliar de marca de Cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="59a0f-121">The required `name` attribute is used as a key to that cache stored for each instance of a Distributed Cache Tag Helper.</span></span>  <span data-ttu-id="59a0f-122">Diferentemente de básica auxiliar de marca de Cache que atribui uma chave para cada instância do auxiliar de marca de Cache com base no nome da página Razor e local do auxiliar de marca na página de razor, o auxiliar de marca de Cache distribuído somente bases chave do atributo`name`</span><span class="sxs-lookup"><span data-stu-id="59a0f-122">Unlike the basic Cache Tag Helper that assigns a key to each Cache Tag Helper instance based on the Razor page name and location of the tag helper in the razor page, the Distributed Cache Tag Helper only bases it's key on the attribute `name`</span></span>

<span data-ttu-id="59a0f-123">Exemplo de uso:</span><span class="sxs-lookup"><span data-stu-id="59a0f-123">Usage Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</Cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="59a0f-124">Distributed implementações de IDistributedCache de auxiliar de marca de Cache</span><span class="sxs-lookup"><span data-stu-id="59a0f-124">Distributed Cache Tag Helper IDistributedCache Implementations</span></span>

<span data-ttu-id="59a0f-125">Há duas implementações de `IDistributedCache` interna do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="59a0f-125">There are two implementations of `IDistributedCache` built in to ASP.NET Core.</span></span>  <span data-ttu-id="59a0f-126">Uma é baseada em **do Sql Server** e a outra é baseada em **Redis**.</span><span class="sxs-lookup"><span data-stu-id="59a0f-126">One is based on **Sql Server** and the other is based on **Redis**.</span></span> <span data-ttu-id="59a0f-127">Os detalhes dessas implementações podem ser encontrados no recurso referenciado abaixo nomeada "Trabalhando com um cache distribuído".</span><span class="sxs-lookup"><span data-stu-id="59a0f-127">Details of these implementations can be found at the resource referenced below named "Working with a distributed cache".</span></span> <span data-ttu-id="59a0f-128">Ambas as implementações envolvem a definição de uma instância de `IDistributedCache` no ASP.NET Core **startup.cs**.</span><span class="sxs-lookup"><span data-stu-id="59a0f-128">Both implementations involve setting an instance of `IDistributedCache` in ASP.NET Core's **startup.cs**.</span></span>

<span data-ttu-id="59a0f-129">Há nenhum atributo de marca especificamente associadas usando qualquer implementação específica de `IDistributedCache`.</span><span class="sxs-lookup"><span data-stu-id="59a0f-129">There no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>



- - -



## <a name="additional-resources"></a><span data-ttu-id="59a0f-130">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="59a0f-130">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper>
* <xref:fundamentals/dependency-injection#service-lifetimes-and-registration-options>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>