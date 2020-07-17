# Router4Delphi
Framework para Criação de Rotas de Telas para FMX

O Router4Delphi tem o objetivo de facilitar a chamada de telas e embed de Layouts em aplicações FMX, reduzindo o acoplamento das telas de dando mais dinâmismo e práticidade na construção de interfaces ricas em Delphi

## Instalação

Basta registrar no Library Path do seu Delphi o caminho da pasta SRC da Biblioteca

## Primeiros Passos

Para utilizar o Router4Delphi para criar suas rotas, você deve realizar a uses do Router4D.

## Criação de uma Tela para Roteamento

Para que o sistema  de Rotas funcione você deve criar um novo formulário FMX e Implementar a Interface iRouter4DComponent ela pertence a unit Router4D.Interfaces portanto a mesma deve ser incluida nas suas Units.

Toda a construção das telas baseadas em rotas utilizar TLayouts para embedar as chamadas das telas, dessa forma é preciso que sua nova tela tenha um TLayout principal e todos os demais componentes devem ser incluídos dentro desse layout.

A Implementação da Interface iRouter4DComponent requer a declaração de dois métodos ( Render e UnRender ), o Render é chamado sempre que uma rota aciona a tela, e o UnRender sempre que ela saí de exibição.

Abaixo o Código de uma tela simples implementando a interface iRouter4DComponent e pronta para ser utilizada.

Crie um Novo Formulario na sua Aplicação, inclua nele um Layout alinhado AlClient e implemente os métodos como abaixo.

```delphi

unit PrimeiraTela;

interface

uses
  System.SysUtils, 
  System.Types, 
  System.UITypes, 
  System.Classes, 
  System.Variants,
  FMX.Types, 
  FMX.Controls, 
  FMX.Forms, 
  FMX.Graphics, 
  FMX.Dialogs,
  Router4D.Interfaces;

type
  TPrimeiraTela = class(TForm, iRouter4DComponent)
    Layout1: TLayout;
  private
    { Private declarations }
  public
    { Public declarations }
    function Render : TFMXObject;
    procedure UnRender;
  end;

var
  PrimeiraTela: TPrimeiraTela;

implementation

{$R *.fmx}

{ TForm3 }

function TPrimeiraTela.Render: TFMXObject;
begin
  Result := Layout1;
end;

procedure TPrimeiraTela.UnRender;
begin

end;

end.
```

Perceba que no método Render nós definimos como Result o Layout1, isso é necessário pois esse layout será embedado sempre que a rota for acionada.

## Registrando a Rota para a Tela

Agora que já temos uma tela pronta para ser registrada vamos ao processo que deixará a nossa tela pronta para ser acionada a qualquer momento.

Para registrar uma rota é necessário declarar a Uses Router4D ela fornece acesso a todos os métodos da biblioteca e em muito dos casos será o único acoplamento necessário nas suas Views.

Uma vez declarada basta acionar o método abaixo para declarar o form que criamos anteriormente como uma rota.

No formPrincipal da sua Aplicação, dentro do método onCreate execute o método abaixo para registrar a Rota para o Form TPrimeiraTela

```delphi

procedure TformPrincipal.FormCreate(Sender: TObject);
begin 
    TRouter4D.Switch.Router('Inicio', TPrimeiraTela);
end;
```

Pronto já temos uma Rota criada, dessa forma os nossos forms não precisam mais conhecer a uses da nossa tela, basta acionar nosso sistema de rotas e pedir a criação da rota "Inicio" que a mesma será exibida no LayoutMain da aplicação.

Você pode criar uma Unit Separada somente para Registrar as Rotas ou então chamar um metodo no onCreate do seu formulario principal para isso.

## Definindo o Render Principal

Já temos uma tela e uma rota para utilizarmos, agora precisamos definir apenas onde está rota renderizará o layout, ou seja, qual será o nosso Objeto que vai receber as telas embedadas.

Para isso no formPrincipal da sua aplicação, declare a uses Router4D e no onCreate do mesmo faça a seguinte chamada.

Lembrando que no passo anterios nós já tinhamos usado o onCreate do formPrincipal para Registrar a Rota.

```delphi

procedure TformPrincipal.FormCreate(Sender: TObject);
begin  
    TRouter4D.Switch.Router('Inicio', TPrimeiraTela);

    TRouter4D.Render<TPrimeiraTela>.SetElement(Layout1, Layout1);
end;

```

O método Render é responsável por definir na biblioteca quais serão os LayoutsMain e Index da Aplicação.

O Render recebe como genéric o nome da Classe da nossa tela inicial, ela será renderizada quando a aplicação abrir dentro do Layout que foi informado como primeiro parametro do SetElement

O primeiro parametro do SetElement está definindo em qual Layout a biblioteca irá renderizar uma nova tela sempre que um Link da rota for chamado.

O Segundo parametro do SetElement está definindo qual é o layout Index da aplicação, assim quando um IndexLink for chamado ele será renderizado nesse layout, mais para frente explicarei sobre o IndexLink.

Pronto, agora ao abrir a sua aplicação você já terá o Layout do Formulario TPrimeiraTela sendo renderizado dentro do Layout1 do formPrincipal da sua aplicação.

## Criando uma Segunda Tela

Para que possamos ver o componente em ação de fato e todos os seus benefícios, crie uma nova tela semelhante a que fizemos no inicio, adicionando um Layout alClient nela e implementando os métodos Render e UnRender.

Coloque dentro do Layout um Label por exemplo, escrito segunda tela apenas para termos a certeza que tudo funcionou corretamente.

```delphi

unit SegundaTela;

interface

uses
  System.SysUtils, 
  System.Types, 
  System.UITypes, 
  System.Classes, 
  System.Variants,
  FMX.Types, 
  FMX.Controls, 
  FMX.Forms, 
  FMX.Graphics, 
  FMX.Dialogs,
  Router4D.Interfaces;

type
  TSegundaTela = class(TForm, iRouter4DComponent)
    Layout1: TLayout;
  private
    { Private declarations }
  public
    { Public declarations }
    function Render : TFMXObject;
    procedure UnRender;
  end;

var
  SegundaTela: TSegundaTela;

implementation

{$R *.fmx}

{ TSegundaTela }

function TSegundaTela.Render: TFMXObject;
begin
  Result := Layout1;
end;

procedure TSegundaTela.UnRender;
begin

end;

end.
```
## Registrando a Segunda tela na Rota

Agora que criamos uma nova tela precisamos registrar ela no sistema de Rotas, então vamos voltar ao onCreate e fazer esse registros, vamos chamar essa tela de Tela2.

```delphi

procedure TformPrincipal.FormCreate(Sender: TObject);
begin  
    TRouter4D.Switch.Router('Inicio', TPrimeiraTela);

    TRouter4D.Switch.Router('Tela2', TSegundaTela);

    TRouter4D.Render<TPrimeiraTela>.SetElement(Layout1, Layout1);
end;

```


## Acionando a nova tela atráves da Rota utilizando o Link

Agora que vem a mágica, volte na TPrimeiraTela e coloque um botão lá e vamos usar o sistema de Links do Router4D para chamar a TSegundaTela sem precisar dar uses nela.

Basta chamar o método abaixo no Evento de Clique do Botão.

```delphi
procedure TPrimeiraTela.Button1Click(Sender: TObject);
begin
  TRouter4D.Link.&To('Tela2');
end;
```

Perceba que a TPrimeiraTela não conhece a TSegundaTela pois o uses da mesma foi dado apenas no formPrincipal onde é necessário para o Registro das Rotas.

Se você deseja deixar isso mais organizado, eu sugiro inclusive que você crie uma Unit separada apenas para registro das Rotas com um class procedure e faça a chamada desse método no onCreate do formPrincipal.

Dessa forma damos fim a um monte de referencias cruzadas e acoplamento entre as telas.


