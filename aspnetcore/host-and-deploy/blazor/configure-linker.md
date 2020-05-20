---
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Configurar o vinculador para ASP.NET CoreBlazor

Por [Luke Latham](https://github.com/guardrex)

BlazorO Webassembly executa a vinculação de [linguagem intermediária (Il)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação para cortar o Il desnecessário dos assemblies de saída do aplicativo. O vinculador está desabilitado ao compilar na configuração de depuração. Os aplicativos devem compilar na configuração de versão para habilitar o vinculador. É recomendável criar em versão ao implantar seus Blazor aplicativos Webassembly. 

A vinculação de um aplicativo otimiza o tamanho, mas pode ter efeitos prejudiciais. Os aplicativos que usam reflexão ou recursos dinâmicos relacionados podem interromper quando cortados porque o vinculador não conhece esse comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão em tempo de execução. Para cortar esses aplicativos, o vinculador deve ser informado sobre quaisquer tipos exigidos pela reflexão no código e em pacotes ou estruturas das quais o aplicativo depende. 

Para garantir que o aplicativo cortado funcione corretamente depois de implantado, é importante testar as compilações de versão do aplicativo com frequência durante o desenvolvimento.

A vinculação de Blazor aplicativos pode ser configurada usando estes recursos do MSBuild:

* Configure o vínculo global com uma [Propriedade do MSBuild](#control-linking-with-an-msbuild-property).
* Controle a vinculação em uma base por assembly com um [arquivo de configuração](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Vinculação de controle com uma propriedade do MSBuild

A vinculação é habilitada quando um aplicativo é criado na `Release` configuração. Para alterar isso, configure a `BlazorWebAssemblyEnableLinking` Propriedade MSBuild no arquivo de projeto:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Controlar a vinculação com um arquivo de configuração

Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*LinkerConfig. xml*:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

Para obter mais informações e exemplos, consulte [formatos de dados (repositório do GitHub mono/Linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Adicionar um arquivo de configuração do vinculador XML a uma biblioteca

Para configurar o vinculador para uma biblioteca específica, adicione um arquivo de configuração do vinculador XML à biblioteca como um recurso incorporado. O recurso inserido deve ter o mesmo nome que o assembly.

No exemplo a seguir, o arquivo *LinkerConfig. xml* é especificado como um recurso inserido que tem o mesmo nome que o assembly da biblioteca:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Configurar o vinculador para internacionalização

Por padrão, a Blazor configuração do vinculador para Blazor aplicativos Webassembly retira informações de internacionalização, exceto as localidades explicitamente solicitadas. Remover esses assemblies minimiza o tamanho do aplicativo.

Para controlar quais assemblies de I18N são retidos, defina a `<BlazorWebAssemblyI18NAssemblies>` Propriedade MSBuild no arquivo de projeto:

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| Valor da região     | Assembly de região mono    |
| ---
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-------- | título do---: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------------ | | `all`            | Todos os assemblies incluídos | | `cjk`             |  *I18n. CJK. dll* | | `mideast`         |  *I18n. Oriente Médio. dll* | | `none`(padrão) | Nenhum | | `other`           |  *I18n. Other. dll* | | `rare`            |  *I18n. Raras. dll* | | `west`            |  *I18n. West. dll*         |

Use uma vírgula para separar vários valores (por exemplo, `mideast,west` ).

Para obter mais informações, consulte [i18n: Pnetlib Internacionalization Framework library (repositório do GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
