## <a name="share-interop-code-in-a-class-library"></a>Compartilhar código interop em uma biblioteca de classes

O código interop JS pode ser incluído em uma biblioteca de classes, o que permite compartilhar o código em um pacote NuGet.

A biblioteca de classe lida com a incorporação de recursos JavaScript no conjunto construído. Os arquivos JavaScript são colocados na pasta *wwwroot.* A ferramentação cuida da incorporação dos recursos quando a biblioteca é construída.

O pacote NuGet construído é referenciado no arquivo de projeto do aplicativo da mesma forma que qualquer pacote NuGet é referenciado. Depois que o pacote for restaurado, o código do aplicativo pode chamar o JavaScript como se fosse C#.

Para obter mais informações, consulte <xref:blazor/class-libraries>.
