---
title: Páginas de ajuda da API Web ASP.NET Core com o Swagger/OpenAPI
author: RicoSuter
description: Este tutorial oferece um passo a passo de como adicionar o Swagger para gerar a documentação e as páginas de ajuda para um aplicativo de API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 66b8278e84df5ee56582254ebe2dc99ada98a9dc
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060300"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="b59c9-103">Páginas de ajuda da API Web ASP.NET Core com o Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="b59c9-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="b59c9-104">Por [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="b59c9-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="b59c9-105">Ao consumir uma API Web, entender seus diversos métodos pode ser desafiador para um desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="b59c9-105">When consuming a web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="b59c9-106">O [Swagger](https://swagger.io/), também conhecido como [openapi](https://www.openapis.org/), resolve o problema de geração de páginas de ajuda e documentação úteis para APIs da Web.</span><span class="sxs-lookup"><span data-stu-id="b59c9-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="b59c9-107">Ele oferece benefícios, como documentação interativa, geração de SDK de cliente e capacidade de descoberta de API.</span><span class="sxs-lookup"><span data-stu-id="b59c9-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="b59c9-108">Neste artigo, são exibidas as implementações do .NET do Swagger [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) e [NSwag](https://github.com/RicoSuter/NSwag):</span><span class="sxs-lookup"><span data-stu-id="b59c9-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="b59c9-109">O **Swashbuckle.AspNetCore** é um projeto de software livre para geração de documentos do Swagger para APIs Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b59c9-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="b59c9-110">O **NSwag** é outro projeto de software livre para geração de documentos do Swagger e a integração da [interface do usuário do Swagger](https://swagger.io/swagger-ui/) ou do [ReDoc](https://github.com/Rebilly/ReDoc) em APIs Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b59c9-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="b59c9-111">Além disso, o NSwag oferece abordagens para gerar o código de cliente C# e TypeScript para sua API.</span><span class="sxs-lookup"><span data-stu-id="b59c9-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="b59c9-112">O que é o Swagger / OpenAPI?</span><span class="sxs-lookup"><span data-stu-id="b59c9-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="b59c9-113">O Swagger é uma especificação independente de linguagem para descrever APIs [REST](https://en.wikipedia.org/wiki/Representational_state_transfer).</span><span class="sxs-lookup"><span data-stu-id="b59c9-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="b59c9-114">O projeto de Swagger foi doado para a [Iniciativa OpenAPI](https://www.openapis.org/), na qual agora é chamado de OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b59c9-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="b59c9-115">Ambos os nomes são intercambiáveis, mas OpenAPI é o preferencial.</span><span class="sxs-lookup"><span data-stu-id="b59c9-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="b59c9-116">Ele permite que computadores e pessoas entendam os recursos de um serviço sem nenhum acesso direto à implementação (código-fonte, acesso à rede, documentação).</span><span class="sxs-lookup"><span data-stu-id="b59c9-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="b59c9-117">Uma meta é minimizar a quantidade de trabalho necessário para conectar-se a serviços desassociados.</span><span class="sxs-lookup"><span data-stu-id="b59c9-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="b59c9-118">Outra meta é reduzir o período de tempo necessário para documentar um serviço com precisão.</span><span class="sxs-lookup"><span data-stu-id="b59c9-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="b59c9-119">Especificação OpenAPI (openapi.jsem)</span><span class="sxs-lookup"><span data-stu-id="b59c9-119">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="b59c9-120">O núcleo para o fluxo OpenAPI é a especificação &mdash; por padrão, um documento chamado *openapi.jsem*.</span><span class="sxs-lookup"><span data-stu-id="b59c9-120">The core to the OpenAPI flow is the specification&mdash;by default, a document named *openapi.json*.</span></span> <span data-ttu-id="b59c9-121">Ele é gerado pela cadeia de ferramentas OpenAPI (ou implementações de terceiros) com base em seu serviço.</span><span class="sxs-lookup"><span data-stu-id="b59c9-121">It's generated by the OpenAPI tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="b59c9-122">Ele descreve os recursos da API e como acessá-lo com HTTP.</span><span class="sxs-lookup"><span data-stu-id="b59c9-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="b59c9-123">Ele gera a interface do usuário do Swagger e é usado pela cadeia de ferramentas para habilitar a geração e a descoberta de código de cliente.</span><span class="sxs-lookup"><span data-stu-id="b59c9-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="b59c9-124">Aqui está um exemplo de uma especificação OpenAPI, reduzida para fins de brevidade:</span><span class="sxs-lookup"><span data-stu-id="b59c9-124">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a><span data-ttu-id="b59c9-125">IU do Swagger</span><span class="sxs-lookup"><span data-stu-id="b59c9-125">Swagger UI</span></span>

<span data-ttu-id="b59c9-126">A [interface do usuário do Swagger](https://swagger.io/swagger-ui/) oferece uma interface do usuário baseada na Web que fornece informações sobre o serviço, usando a especificação openapi gerada.</span><span class="sxs-lookup"><span data-stu-id="b59c9-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="b59c9-127">O Swashbuckle e o NSwag incluem uma versão incorporada da interface do usuário do Swagger, para que ele possa ser hospedado em seu aplicativo ASP.NET Core usando uma chamada de registro de middleware.</span><span class="sxs-lookup"><span data-stu-id="b59c9-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="b59c9-128">A interface do usuário da Web tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="b59c9-128">The web UI looks like this:</span></span>

![IU do Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="b59c9-130">Todo método de ação pública nos controladores pode ser testado da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b59c9-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="b59c9-131">Clique em um nome de método para expandir a seção.</span><span class="sxs-lookup"><span data-stu-id="b59c9-131">Click a method name to expand the section.</span></span> <span data-ttu-id="b59c9-132">Adicione todos os parâmetros necessários e clique em **Experimente!**.</span><span class="sxs-lookup"><span data-stu-id="b59c9-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Teste GET de Swagger de exemplo](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="b59c9-134">A versão da interface do usuário do Swagger usada para as capturas de tela é a versão 2.</span><span class="sxs-lookup"><span data-stu-id="b59c9-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="b59c9-135">Para obter um exemplo da versão 3, confira [Exemplo Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="b59c9-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b59c9-136">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="b59c9-136">Next steps</span></span>

* [<span data-ttu-id="b59c9-137">Introdução ao Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="b59c9-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="b59c9-138">Introdução ao NSwag</span><span class="sxs-lookup"><span data-stu-id="b59c9-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
