---
title: "Integração de teste no núcleo do ASP.NET"
author: ardalis
description: "Como usar a integração do ASP.NET Core testes para garantir que os componentes de um aplicativo funcionem corretamente."
keywords: "ASP.NET Core, testes de integração"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 40d534f2-89b3-4b09-9c2c-3494bf9991c9
ms.technology: aspnet
ms.prod: asp.net-core
uid: testing/integration-testing
ms.openlocfilehash: d30af6edc31fbce2ebb77c57be3fd78231c54b50
ms.sourcegitcommit: 418e6aa4ab79474ecc4d0a6af573a3759b113fe4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2017
---
# <a name="integration-testing-in-aspnet-core"></a><span data-ttu-id="287b4-104">Integração de teste no núcleo do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="287b4-104">Integration testing in ASP.NET Core</span></span>

<span data-ttu-id="287b4-105">Por [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="287b4-105">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="287b4-106">Testes de integração garantem que componentes de um aplicativo funcionam corretamente quando montado juntos.</span><span class="sxs-lookup"><span data-stu-id="287b4-106">Integration testing ensures that an application's components function correctly when assembled together.</span></span> <span data-ttu-id="287b4-107">ASP.NET Core dá suporte a teste de integração usando estruturas de teste de unidade e um host da web internos de teste que pode ser usado para tratar as solicitações sem sobrecarga de rede.</span><span class="sxs-lookup"><span data-stu-id="287b4-107">ASP.NET Core supports integration testing using unit test frameworks and a built-in test web host that can be used to handle requests without network overhead.</span></span>

[<span data-ttu-id="287b4-108">Exibir ou baixar o código de exemplo</span><span class="sxs-lookup"><span data-stu-id="287b4-108">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/testing/integration-testing/sample)

## <a name="introduction-to-integration-testing"></a><span data-ttu-id="287b4-109">Introdução ao teste de integração</span><span class="sxs-lookup"><span data-stu-id="287b4-109">Introduction to integration testing</span></span>

<span data-ttu-id="287b4-110">Testes de integração verificar que diferentes partes de um aplicativo funcionem corretamente.</span><span class="sxs-lookup"><span data-stu-id="287b4-110">Integration tests verify that different parts of an application work correctly together.</span></span> <span data-ttu-id="287b4-111">Ao contrário de [testes de unidade](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test), testes de integração com frequência envolvem preocupações de infraestrutura de aplicativo, como um banco de dados, sistema de arquivos, recursos de rede, ou web solicitações e respostas.</span><span class="sxs-lookup"><span data-stu-id="287b4-111">Unlike [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test), integration tests frequently involve application infrastructure concerns, such as a database, file system, network resources, or web requests and responses.</span></span> <span data-ttu-id="287b4-112">Testes de unidade usam falsificações ou objetos fictícios no lugar essas preocupações, mas a finalidade de testes de integração é confirmar se o sistema está funcionando conforme o esperado com esses sistemas.</span><span class="sxs-lookup"><span data-stu-id="287b4-112">Unit tests use fakes or mock objects in place of these concerns, but the purpose of integration tests is to confirm that the system works as expected with these systems.</span></span>

<span data-ttu-id="287b4-113">Testes de integração, porque praticados maior de segmentos de código e como eles se basear em elementos de infraestrutura, tendem a ser ordens de magnitude menor do que testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="287b4-113">Integration tests, because they exercise larger segments of code and because they rely on infrastructure elements, tend to be orders of magnitude slower than unit tests.</span></span> <span data-ttu-id="287b4-114">Portanto, é uma boa ideia limitar quantos testes de integração que você escreve, especialmente se você pode testar o mesmo comportamento com um teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="287b4-114">Thus, it's a good idea to limit how many integration tests you write, especially if you can test the same behavior with a unit test.</span></span>

> [!NOTE]
> <span data-ttu-id="287b4-115">Se algum comportamento pode ser testado usando um teste de unidade ou um teste de integração, prefira o teste de unidade, já que ele é quase sempre ser mais rápido.</span><span class="sxs-lookup"><span data-stu-id="287b4-115">If some behavior can be tested using either a unit test or an integration test, prefer the unit test, since it will be almost always be faster.</span></span> <span data-ttu-id="287b4-116">Você pode ter dúzias ou centenas de testes de unidade com muitas entradas diferentes, mas apenas uma série de testes de integração que cobrem os cenários mais importantes.</span><span class="sxs-lookup"><span data-stu-id="287b4-116">You might have dozens or hundreds of unit tests with many different inputs but just a handful of integration tests covering the most important scenarios.</span></span>

<span data-ttu-id="287b4-117">A lógica dentro de seus próprios métodos de teste é geralmente o domínio de testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="287b4-117">Testing the logic within your own methods is usually the domain of unit tests.</span></span> <span data-ttu-id="287b4-118">Testar o funcionamento do seu aplicativo em sua estrutura, por exemplo, com ASP.NET Core, ou com um banco de dados é onde os testes de integração entram em ação.</span><span class="sxs-lookup"><span data-stu-id="287b4-118">Testing how your application works within its framework, for example with ASP.NET Core, or with a database is where integration tests come into play.</span></span> <span data-ttu-id="287b4-119">Ele não tem muitos testes de integração para confirmar que você é capaz de gravar uma linha para o banco de dados e lê-lo novamente.</span><span class="sxs-lookup"><span data-stu-id="287b4-119">It doesn't take too many integration tests to confirm that you're able to write a row to the database and read it back.</span></span> <span data-ttu-id="287b4-120">Não é necessário testar cada permutação possíveis do seu código de acesso de dados - você precisa testar suficiente para que você a certeza de que seu aplicativo está funcionando corretamente.</span><span class="sxs-lookup"><span data-stu-id="287b4-120">You don't need to test every possible permutation of your data access code - you only need to test enough to give you confidence that your application is working properly.</span></span>

## <a name="integration-testing-aspnet-core"></a><span data-ttu-id="287b4-121">Integração de teste ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="287b4-121">Integration testing ASP.NET Core</span></span>

<span data-ttu-id="287b4-122">Para obter configurou a testes de integração de execução, você precisará criar um projeto de teste, adicione uma referência ao projeto web ASP.NET Core e instalar um executor de teste.</span><span class="sxs-lookup"><span data-stu-id="287b4-122">To get set up to run integration tests, you'll need to create a test project, add a reference to your ASP.NET Core web project, and install a test runner.</span></span> <span data-ttu-id="287b4-123">Esse processo é descrito no [testes de unidade](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentação, junto com instruções mais detalhadas sobre como executar testes e recomendações para nomear suas classes de teste e testes.</span><span class="sxs-lookup"><span data-stu-id="287b4-123">This process is described in the [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation, along with more detailed instructions on running tests and recommendations for naming your tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="287b4-124">Separe os testes de unidade e os testes de integração usando projetos diferentes.</span><span class="sxs-lookup"><span data-stu-id="287b4-124">Separate your unit tests and your integration tests using different projects.</span></span> <span data-ttu-id="287b4-125">Isso ajuda a garantir que você não acidentalmente introduzir problemas de infraestrutura em seus testes de unidade e permite que você escolher facilmente qual conjunto de testes para executar.</span><span class="sxs-lookup"><span data-stu-id="287b4-125">This helps ensure you don't accidentally introduce infrastructure concerns into your unit tests and lets you easily choose which set of tests to run.</span></span>

### <a name="the-test-host"></a><span data-ttu-id="287b4-126">O Host de teste</span><span class="sxs-lookup"><span data-stu-id="287b4-126">The Test Host</span></span>

<span data-ttu-id="287b4-127">ASP.NET Core inclui um host de teste que pode ser adicionado a projetos de teste de integração e usado para hospedar o ASP.NET Core aplicativos, solicitações de serviço de teste sem a necessidade de um host da web real.</span><span class="sxs-lookup"><span data-stu-id="287b4-127">ASP.NET Core includes a test host that can be added to integration test projects and used to host ASP.NET Core applications, serving test requests without the need for a real web host.</span></span> <span data-ttu-id="287b4-128">O exemplo fornecido inclui um projeto de teste de integração que foi configurado para usar [xUnit](https://xunit.github.io) e o Host de teste.</span><span class="sxs-lookup"><span data-stu-id="287b4-128">The provided sample includes an integration test project which has been configured to use [xUnit](https://xunit.github.io) and the Test Host.</span></span> <span data-ttu-id="287b4-129">Ele usa o `Microsoft.AspNetCore.TestHost` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="287b4-129">It uses the `Microsoft.AspNetCore.TestHost` NuGet package.</span></span>

<span data-ttu-id="287b4-130">Uma vez o `Microsoft.AspNetCore.TestHost` pacote está incluído no projeto, você poderá criar e configurar um `TestServer` em seus testes.</span><span class="sxs-lookup"><span data-stu-id="287b4-130">Once the `Microsoft.AspNetCore.TestHost` package is included in the project, you'll be able to create and configure a `TestServer` in your tests.</span></span> <span data-ttu-id="287b4-131">O teste a seguir mostra como verificar uma solicitação feita para a raiz de um site retorna "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="287b4-131">The following test shows how to verify that a request made to the root of a site returns "Hello World!"</span></span> <span data-ttu-id="287b4-132">e deve ser executado com êxito em relação ao padrão modelo de Web do ASP.NET Core vazio criado pelo Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="287b4-132">and should run successfully against the default ASP.NET Core Empty Web template created by Visual Studio.</span></span>

<span data-ttu-id="287b4-133">[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebDefaultRequestShould.cs?name=snippet_WebDefault&highlight=7,16,22)]</span><span class="sxs-lookup"><span data-stu-id="287b4-133">[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebDefaultRequestShould.cs?name=snippet_WebDefault&highlight=7,16,22)]</span></span>

<span data-ttu-id="287b4-134">Este teste é usando o padrão de organizar Act declaração.</span><span class="sxs-lookup"><span data-stu-id="287b4-134">This test is using the Arrange-Act-Assert pattern.</span></span> <span data-ttu-id="287b4-135">A etapa de organizar é feita no construtor, que cria uma instância de `TestServer`.</span><span class="sxs-lookup"><span data-stu-id="287b4-135">The Arrange step is done in the constructor, which creates an instance of `TestServer`.</span></span> <span data-ttu-id="287b4-136">Configurado `WebHostBuilder` será usado para criar um `TestHost`; neste exemplo, o `Configure` método do sistema em teste (SUT) `Startup` classe é passada para o `WebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="287b4-136">A configured `WebHostBuilder` will be used to create a `TestHost`; in this example, the `Configure` method from the system under test (SUT)'s `Startup` class is passed to the `WebHostBuilder`.</span></span> <span data-ttu-id="287b4-137">Esse método será usado para configurar o pipeline de solicitação do `TestServer` identicamente a como o servidor SUT deve ser configurado.</span><span class="sxs-lookup"><span data-stu-id="287b4-137">This method will be used to configure the request pipeline of the `TestServer` identically to how the SUT server would be configured.</span></span>

<span data-ttu-id="287b4-138">Na parte do Act do teste, é feita uma solicitação para a `TestServer` instância para o caminho "/" e a resposta é lido novamente em uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="287b4-138">In the Act portion of the test, a request is made to the `TestServer` instance for the "/" path, and the response is read back into a string.</span></span> <span data-ttu-id="287b4-139">Essa cadeia de caracteres é comparada com a cadeia de caracteres esperada de "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="287b4-139">This string is compared with the expected string of "Hello World!".</span></span> <span data-ttu-id="287b4-140">Se elas corresponderem, o teste seja aprovado; Caso contrário, ele falhará.</span><span class="sxs-lookup"><span data-stu-id="287b4-140">If they match, the test passes; otherwise, it fails.</span></span>

<span data-ttu-id="287b4-141">Agora você pode adicionar alguns testes de integração adicionais para confirmar que o primo verificando funcionalidade funciona por meio do aplicativo web:</span><span class="sxs-lookup"><span data-stu-id="287b4-141">Now you can add a few additional integration tests to confirm that the prime checking functionality works via the web application:</span></span>

<span data-ttu-id="287b4-142">[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebCheckPrimeShould.cs?name=snippet_CheckPrime)]</span><span class="sxs-lookup"><span data-stu-id="287b4-142">[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebCheckPrimeShould.cs?name=snippet_CheckPrime)]</span></span>

<span data-ttu-id="287b4-143">Observe que não é realmente está tentando testar a exatidão do verificador de número primo com esses testes, mas em vez disso, que o aplicativo web está fazendo o que você espera.</span><span class="sxs-lookup"><span data-stu-id="287b4-143">Note that you're not really trying to test the correctness of the prime number checker with these tests but rather that the web application is doing what you expect.</span></span> <span data-ttu-id="287b4-144">Você já tem cobertura de teste de unidade que proporciona confiança em `PrimeService`, como você pode ver aqui:</span><span class="sxs-lookup"><span data-stu-id="287b4-144">You already have unit test coverage that gives you confidence in `PrimeService`, as you can see here:</span></span>

![Gerenciador de Testes](integration-testing/_static/test-explorer.png)

<span data-ttu-id="287b4-146">Você pode aprender mais sobre os testes de unidade no [testes de unidade](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) artigo.</span><span class="sxs-lookup"><span data-stu-id="287b4-146">You can learn more about the unit tests in the [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) article.</span></span>

## <a name="refactoring-to-use-middleware"></a><span data-ttu-id="287b4-147">Para usar o middleware de refatoração</span><span class="sxs-lookup"><span data-stu-id="287b4-147">Refactoring to use middleware</span></span>

<span data-ttu-id="287b4-148">Refatoração é o processo de alteração de código do aplicativo para melhorar seu design sem alterar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="287b4-148">Refactoring is the process of changing an application's code to improve its design without changing its behavior.</span></span> <span data-ttu-id="287b4-149">Idealmente deve ser feito quando há um conjunto de passar os testes, pois esses ajuda garantir o que comportamento do sistema permanece a mesma antes e após as alterações.</span><span class="sxs-lookup"><span data-stu-id="287b4-149">It should ideally be done when there is a suite of passing tests, since these help ensure the system's behavior remains the same before and after the changes.</span></span> <span data-ttu-id="287b4-150">Examinar a maneira na qual o primo lógica de verificação é implementado no aplicativo da web `Configure` método, consulte:</span><span class="sxs-lookup"><span data-stu-id="287b4-150">Looking at the way in which the prime checking logic is implemented in the web application's `Configure` method, you see:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.Run(async (context) =>
    {
        if (context.Request.Path.Value.Contains("checkprime"))
        {
            int numberToCheck;
            try
            {
                numberToCheck = int.Parse(context.Request.QueryString.Value.Replace("?", ""));
                var primeService = new PrimeService();
                if (primeService.IsPrime(numberToCheck))
                {
                    await context.Response.WriteAsync($"{numberToCheck} is prime!");
                }
                else
                {
                    await context.Response.WriteAsync($"{numberToCheck} is NOT prime!");
                }
            }
            catch
            {
                await context.Response.WriteAsync("Pass in a number to check in the form /checkprime?5");
            }
        }
        else
        {
            await context.Response.WriteAsync("Hello World!");
        }
    });
}
```

<span data-ttu-id="287b4-151">Esse código funciona, mas está longe de como você deseja implementar esse tipo de funcionalidade em um aplicativo ASP.NET Core, até mesmo simple como este é.</span><span class="sxs-lookup"><span data-stu-id="287b4-151">This code works, but it's far from how you would like to implement this kind of functionality in an ASP.NET Core application, even as simple as this is.</span></span> <span data-ttu-id="287b4-152">Imagine que o `Configure` método seria semelhante, se necessário para adicionar essa quantidade código para ele sempre que você adicionar outro ponto de extremidade de URL!</span><span class="sxs-lookup"><span data-stu-id="287b4-152">Imagine what the `Configure` method would look like if you needed to add this much code to it every time you add another URL endpoint!</span></span>

<span data-ttu-id="287b4-153">Adição de uma opção a ser considerada [MVC](xref:mvc/overview) para o aplicativo e criando um controlador para lidar com a verificação principal.</span><span class="sxs-lookup"><span data-stu-id="287b4-153">One option to consider is adding [MVC](xref:mvc/overview) to the application and creating a controller to handle the prime checking.</span></span> <span data-ttu-id="287b4-154">No entanto, supondo que você atualmente não precisa de qualquer outra MVC funcionalidade, que é um bit exageros.</span><span class="sxs-lookup"><span data-stu-id="287b4-154">However, assuming you don't currently need any other MVC functionality, that's a bit overkill.</span></span>

<span data-ttu-id="287b4-155">No entanto, você pode tirar proveito do ASP.NET Core [middleware](xref:fundamentals/middleware), que nos ajudarão a encapsular o primo verificação lógica em sua própria classe e obter melhor [separação de preocupações](http://deviq.com/separation-of-concerns/) no `Configure` método.</span><span class="sxs-lookup"><span data-stu-id="287b4-155">You can, however, take advantage of ASP.NET Core [middleware](xref:fundamentals/middleware), which will help us encapsulate the prime checking logic in its own class and achieve better [separation of concerns](http://deviq.com/separation-of-concerns/) in the `Configure` method.</span></span>

<span data-ttu-id="287b4-156">Para permitir que o caminho do middleware usa a ser especificado como um parâmetro para a classe de middleware espera um `RequestDelegate` e um `PrimeCheckerOptions` instância em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="287b4-156">You want to allow the path the middleware uses to be specified as a parameter, so the middleware class expects a `RequestDelegate` and a `PrimeCheckerOptions` instance in its constructor.</span></span> <span data-ttu-id="287b4-157">Se o caminho da solicitação não corresponde ao que este middleware configurado para esperar, basta chamar o próximo middleware da cadeia e não fazer nada.</span><span class="sxs-lookup"><span data-stu-id="287b4-157">If the path of the request doesn't match what this middleware is configured to expect, you simply call the next middleware in the chain and do nothing further.</span></span> <span data-ttu-id="287b4-158">O restante do código de implementação que estava no `Configure` está agora no `Invoke` método.</span><span class="sxs-lookup"><span data-stu-id="287b4-158">The rest of the implementation code that was in `Configure` is now in the `Invoke` method.</span></span>

> [!NOTE]
> <span data-ttu-id="287b4-159">Desde que o middleware depende do `PrimeService` serviço, você também está solicitando uma instância do serviço com o construtor.</span><span class="sxs-lookup"><span data-stu-id="287b4-159">Since the middleware depends on the `PrimeService` service, you're also requesting an instance of this service with the constructor.</span></span> <span data-ttu-id="287b4-160">A estrutura oferecer esse serviço por meio de [injeção de dependência](xref:fundamentals/dependency-injection), supondo que ele tiver sido configurado, por exemplo, em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="287b4-160">The framework will provide this service via [Dependency Injection](xref:fundamentals/dependency-injection), assuming it has been configured, for example in `ConfigureServices`.</span></span>

<span data-ttu-id="287b4-161">[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Middleware/PrimeCheckerMiddleware.cs?highlight=39-63)]</span><span class="sxs-lookup"><span data-stu-id="287b4-161">[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Middleware/PrimeCheckerMiddleware.cs?highlight=39-63)]</span></span>

<span data-ttu-id="287b4-162">Como este middleware atua como um ponto de extremidade da cadeia de delegado solicitação quando o caminho corresponde, não há nenhuma chamada para `_next.Invoke` quando este middleware manipula a solicitação.</span><span class="sxs-lookup"><span data-stu-id="287b4-162">Since this middleware acts as an endpoint in the request delegate chain when its path matches, there is no call to `_next.Invoke` when this middleware handles the request.</span></span>

<span data-ttu-id="287b4-163">Com este middleware em vigor e alguns útil métodos de extensão criados para facilitar a configuração, o refatorado `Configure` método tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="287b4-163">With this middleware in place and some helpful extension methods created to make configuring it easier, the refactored `Configure` method looks like this:</span></span>

<span data-ttu-id="287b4-164">[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Startup.cs?highlight=9&range=19-33)]</span><span class="sxs-lookup"><span data-stu-id="287b4-164">[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Startup.cs?highlight=9&range=19-33)]</span></span>

<span data-ttu-id="287b4-165">Seguindo essa refatoração estiver certo de que o aplicativo web ainda funciona como antes, desde que os testes de integração estão passando.</span><span class="sxs-lookup"><span data-stu-id="287b4-165">Following this refactoring, you're confident that the web application still works as before, since your integration tests are all passing.</span></span>

> [!NOTE]
> <span data-ttu-id="287b4-166">É uma boa ideia para confirmar as alterações ao controle de origem depois de concluir uma refatoração e transmitir seus testes.</span><span class="sxs-lookup"><span data-stu-id="287b4-166">It's a good idea to commit your changes to source control after you complete a refactoring and your tests pass.</span></span> <span data-ttu-id="287b4-167">Se você está praticando desenvolvimento orientado por testes, [considere a adição de confirmação para o ciclo de vermelho-verde-refatorar](http://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development).</span><span class="sxs-lookup"><span data-stu-id="287b4-167">If you're practicing Test Driven Development, [consider adding Commit to your Red-Green-Refactor cycle](http://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development).</span></span>

## <a name="resources"></a><span data-ttu-id="287b4-168">Recursos</span><span class="sxs-lookup"><span data-stu-id="287b4-168">Resources</span></span>

* [<span data-ttu-id="287b4-169">Testes de unidade</span><span class="sxs-lookup"><span data-stu-id="287b4-169">Unit testing</span></span>](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="287b4-170">Middleware</span><span class="sxs-lookup"><span data-stu-id="287b4-170">Middleware</span></span>](xref:fundamentals/middleware)
* [<span data-ttu-id="287b4-171">Controladores de teste</span><span class="sxs-lookup"><span data-stu-id="287b4-171">Testing controllers</span></span>](xref:mvc/controllers/testing)