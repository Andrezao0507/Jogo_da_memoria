# Jogo_da_memoria
unit principal;

interface

uses
  Winapi.Windows, Winapi.Messages, System.SysUtils, System.Variants, System.Classes, Vcl.Graphics,
  Vcl.Controls, Vcl.Forms, Vcl.Dialogs, Vcl.ExtCtrls, Data.DB, Vcl.Grids,
  Vcl.DBGrids, Vcl.StdCtrls, Vcl.Imaging.pngimage, FireDAC.Stan.Intf,
  FireDAC.Stan.Option, FireDAC.Stan.Error, FireDAC.UI.Intf, FireDAC.Phys.Intf,
  FireDAC.Stan.Def, FireDAC.Stan.Pool, FireDAC.Stan.Async, FireDAC.Phys,
  FireDAC.Phys.SQLite, FireDAC.Phys.SQLiteDef, FireDAC.Stan.ExprFuncs,
  FireDAC.VCLUI.Wait, FireDAC.Stan.Param, FireDAC.DatS, FireDAC.DApt.Intf,
  FireDAC.DApt, FireDAC.Phys.SQLiteWrapper.Stat, FireDAC.Comp.DataSet,
  FireDAC.Comp.Client;

type
  TForm1 = class(TForm)
    pnTop: TPanel;
    Label1: TLabel;
    edtPlayer: TEdit;
    Panel2: TPanel;
    btnIniciar: TButton;
    btnPausar: TButton;
    btnRecomecar: TButton;
    Panel3: TPanel;
    Panel4: TPanel;
    Panel5: TPanel;
    Panel6: TPanel;
    Label2: TLabel;
    edtQtdJogadas: TEdit;
    Panel7: TPanel;
    Label3: TLabel;
    edttempoJogado: TEdit;
    pnClient: TPanel;
    pnRanking: TPanel;
    pnImagens: TPanel;
    Panel8: TPanel;
    Panel9: TPanel;
    Panel10: TPanel;
    Img4: TImage;
    Img3: TImage;
    Img2: TImage;
    Img1: TImage;
    Img8: TImage;
    Img7: TImage;
    Img6: TImage;
    Img5: TImage;
    Img12: TImage;
    Img11: TImage;
    Img10: TImage;
    Img9: TImage;
    Panel11: TPanel;
    DBGrid1: TDBGrid;
    pnBase: TPanel;
    img_0: TImage;
    img_1: TImage;
    img_2: TImage;
    img_3: TImage;
    img_4: TImage;
    img_5: TImage;
    img_6: TImage;
    conexao: TFDConnection;
    qrRanking: TFDQuery;
    ds: TDataSource;
    FDPhysSQLiteDriverLink1: TFDPhysSQLiteDriverLink;
    TmContador: TTimer;
    TmComparar: TTimer;
    qrRankingid: TFDAutoIncField;
    qrRankingplayer_name: TStringField;
    qrRankingqtde_jogadas: TIntegerField;
    qrRankingtempo_jogado: TTimeField;
    qrRankingdata_registro: TDateTimeField;
    procedure FormActivate(Sender: TObject);
    procedure btnIniciarClick(Sender: TObject);
    procedure Img1Click(Sender: TObject);
    procedure Img2Click(Sender: TObject);
    procedure Img3Click(Sender: TObject);
    procedure Img4Click(Sender: TObject);
    procedure Img5Click(Sender: TObject);
    procedure Img6Click(Sender: TObject);
    procedure Img7Click(Sender: TObject);
    procedure Img8Click(Sender: TObject);
    procedure Img9Click(Sender: TObject);
    procedure Img10Click(Sender: TObject);
    procedure Img11Click(Sender: TObject);
    procedure Img12Click(Sender: TObject);
    procedure TmCompararTimer(Sender: TObject);
    procedure TmContadorTimer(Sender: TObject);
    procedure btnPausarClick(Sender: TObject);
    procedure btnRecomecarClick(Sender: TObject);
  private
    im1, im2, im3, im4, im5, im6, im7, im8, im9,
    im10, im11, im12, ImagemSelecionada1, ImagemSelecionada2: string;
    Componenteselecionado1, Componenteselecionado2: Timage;

    procedure SortearImagens;

    procedure PreencheFotoPadrao;

    procedure ExibirImagem(pComponenteImagem: Timage; pImagemNome: string);

    procedure RegistrarJogada;

    function JogoTerminou: Boolean;

    procedure ListarRanking;
    procedure RegistarJogada;
  public
  procedure iniciar;
  end;
var
  Form1: TForm1;

implementation

{$R *.dfm}

{ TForm1 }

procedure TForm1.btnIniciarClick(Sender: TObject);
begin
  iniciar;
end;

procedure TForm1.btnPausarClick(Sender: TObject);
begin
  if (btnPausar.caption='Continuar') then
    begin
      btnPausar.caption:='Pausar';
      TmContador.Enabled:= true;
      panel10.enabled:= true;
    end
  else
    begin
      btnPausar.caption:='Continuar';
      TmContador.Enabled:= false;
      panel10.enabled:= false;
    end;
end;

procedure TForm1.btnRecomecarClick(Sender: TObject);
begin
  edttempoJogado.Text:= '00:00:00';
  edtQtdJogadas.Text:='0';
  PreencheFotoPadrao;
end;

procedure TForm1.ExibirImagem(pComponenteImagem: Timage; pImagemNome: string);
begin
  if (trim(pImagemNome) <> '') then
  begin
    pComponenteImagem.Picture :=
      TImage( FindComponent( pImagemNome ) ).Picture;

    //Verifica se já tem uma imagem selecionada
    if (ImagemSelecionada1 = '') then
    begin
      ImagemSelecionada1     := pImagemNome;
      ComponenteSelecionado1 := pComponenteImagem;
    end
    //Caso tiver a primeira imagem, verifica se a segunda já
    // está selecionada tbm
    else if (ImagemSelecionada2 = '') then
    begin
      ImagemSelecionada2     := pImagemNome;
      ComponenteSelecionado2 := pComponenteImagem;

      //Verifica se a mesma imagem recebeu 2 cliques
      if (ComponenteSelecionado1 = ComponenteSelecionado2) then
      begin
        ImagemSelecionada2     := '';
        ComponenteSelecionado2 := nil;
        exit;
      end;

      //Inicia a comparação
      tmComparar.Enabled := true;
    end;
  end;
end;

procedure TForm1.FormActivate(Sender: TObject);
begin
  PreencheFotoPadrao;
  ListarRanking;
end;

procedure TForm1.Img10Click(Sender: TObject);
begin
 ExibirImagem(img10, im10);
end;

procedure TForm1.Img11Click(Sender: TObject);
begin
 ExibirImagem(img11, im11);
end;

procedure TForm1.Img12Click(Sender: TObject);
begin
 ExibirImagem(img12, im12);
end;

procedure TForm1.Img1Click(Sender: TObject);
begin
  ExibirImagem(img1, im1);
end;

procedure TForm1.Img2Click(Sender: TObject);
begin
   ExibirImagem(img2, im2);
end;

procedure TForm1.Img3Click(Sender: TObject);
begin
   ExibirImagem(img3, im3);
end;

procedure TForm1.Img4Click(Sender: TObject);
begin
ExibirImagem(img4, im4);
end;

procedure TForm1.Img5Click(Sender: TObject);
begin
 ExibirImagem(img5, im5);
end;

procedure TForm1.Img6Click(Sender: TObject);
begin
 ExibirImagem(img6, im6);
end;

procedure TForm1.Img7Click(Sender: TObject);
begin
   ExibirImagem(img7, im7);
end;

procedure TForm1.Img8Click(Sender: TObject);
begin
 ExibirImagem(img8, im8);
end;

procedure TForm1.Img9Click(Sender: TObject);
begin
   ExibirImagem(img9, im9);
end;

procedure TForm1.iniciar;
begin
  if (Trim(EdtPlayer.Text)= '') then
  begin
    showmessage('Informe o nome do usuário!');
    //simula o clique com o mouse em cima do componente
    EdtPlayer.setfocus;
    exit;
  end;
  btnIniciar.visible:= false;
  btnPausar.visible:= true;
  btnRecomecar.visible:= true;
  btnPausar.caption:= 'PAUSAR';
  pnImagens.visible:= true;
  edtQtdJogadas.text:= '0';
  edtTempoJogado.Text:= '00:00:00';
  tmcontador.enabled:= true;
  ImagemSelecionada1:= '';
  ImagemSelecionada2:= '';
  PreencheFotoPadrao;
  SortearImagens;
end;

function TForm1.JogoTerminou: Boolean;
begin
  if (img1.Enabled = false) and (img2.Enabled = false) and
    (img3.Enabled = false) and (img4.Enabled = false) and
    (img5.Enabled = false) and (img6.Enabled = false) and
    (img7.Enabled = false) and (img8.Enabled = false) and
    (img9.Enabled = false) and (img10.Enabled = false) and
    (img11.Enabled = false) and (img12.Enabled = false) then
    result := true
  else
    result := false;
end;

procedure TForm1.ListarRanking;
begin
  qrRanking.active:= false;
  qrRanking.SQL.Clear;
  qrRanking.SQL.add('select * from Ranking');
  qrRanking.SQL.add('order by QTDE_JOGADAS, TEMPO_JOGADO, DATA_REGISTRO');
  qrRanking.active:= true;
end;

procedure TForm1.PreencheFotoPadrao;
begin
   img1.Picture:= img_0.Picture;
   img2.Picture:= img_0.Picture;
   img3.Picture:= img_0.Picture;
   img4.Picture:= img_0.Picture;
   img5.Picture:= img_0.Picture;
   img6.Picture:= img_0.Picture;
   img7.Picture:= img_0.Picture;
   img8.Picture:= img_0.Picture;
   img9.Picture:= img_0.Picture;
   img10.Picture:= img_0.Picture;
   img11.Picture:= img_0.Picture;
   img12.Picture:= img_0.Picture;

   img1.enabled:=true;
   img2.enabled:=true;
   img3.enabled:=true;
   img4.enabled:=true;
   img5.enabled:=true;
   img6.enabled:=true;
   img7.enabled:=true;
   img8.enabled:=true;
   img9.enabled:=true;
   img10.enabled:=true;
   img11.enabled:=true;
   img12.enabled:=true;
end;

procedure TForm1.RegistarJogada;
begin

end;

procedure TForm1.RegistrarJogada;
begin
  qrRanking.Insert;
  qrRankingPLAYER_NAME.AsString     := EdtPlayer.Text;
  qrRankingTEMPO_JOGADO.AsDateTime  := StrToTime(edtTempoJogado.Text);
  qrRankingDATA_REGISTRO.AsDateTime := now;
  qrRankingQTDE_JOGADAS.AsInteger   := StrToInt(edtQtdJogadas.Text);
  qrRanking.Post;

 showMessage(

  'Jogo concluído com sucesso!' +sLineBreak+
  'Player: '+edtPlayer.Text +sLineBreak+
  'Tempo jogado: '+ edtTempoJogado.Text +sLineBreak+
  'Jogadas: '+EdtQtdJogadas.Text);
ListarRanking;


end;

procedure TForm1.SortearImagens;
  var
    nomefoto: string;
    codigofoto: integer;
begin
  //limpa o conteúdo de todas as variaveis strings
  im1:= '';
  im2:= '';
  im3:= '';
  im4:= '';
  im5:= '';
  im6:= '';
  im7:= '';
  im8:= '';
  im9:= '';
  im10:= '';
  im11:= '';
  im12:= '';

  //setas todas as propiedades tags de imagens para base 0
  img1.tag:=0;
  img2.tag:=0;
  img3.tag:=0;
  img4.tag:=0;
  img5.tag:=0;
  img6.tag:=0;

  while (true) do
  begin
    codigofoto:= random (6) + 1; //comando para capturar indice da foto de 1 a 6
    nomefoto:= 'img_' + inttostr (codigofoto);
    if (Timage(findcomponent(nomefoto)).tag>=2) then
    begin
      continue; //volta ao loop de vlidaçaõ
    end
      else
      begin
        //soma a propiedade tag da imagem base + 1 no indice
        (TImage(findcomponent(nomefoto))).Tag :=
          (Timage(findcomponent(nomefoto)).Tag + 1);


        //verifica quais imagens foram preenchidas nas variaveis de controle
        if (im1 = '') then
        im1:= nomefoto
        else if (im2 = '') then
        im2:= nomefoto
        else if (im3= '') then
        im3:= nomefoto
        else if (im4 = '') then
        im4:= nomefoto
        else if (im5 = '') then
        im5:= nomefoto
        else if (im6 = '') then
        im6:= nomefoto
        else if (im7 = '') then
        im7:= nomefoto
        else if (im8 = '') then
        im8:= nomefoto
        else if (im9 = '') then
        im9:= nomefoto
        else if (im10 = '') then
        im10:= nomefoto
        else if (im11 = '') then
        im11:= nomefoto
        else if (im12 = '') then
        begin
          im12:=nomefoto;
          break;//finaliza o loop
        end;
      end;
  end;
end;

procedure TForm1.TmCompararTimer(Sender: TObject);
var qtdeJogadas : Integer;
begin
  //Pausa o timer de comparação
  tmComparar.Enabled := false;

  if (ImagemSelecionada1 = ImagemSelecionada2) then
  begin
    //CASO AS IMAGENS FOREM IGUAIS, BLOQUEAMOS
    //ELAS PARA NÃO PODEREM SER CLICADAS NOVAMENTE
    ComponenteSelecionado1.Enabled := false;
    ComponenteSelecionado2.Enabled := false;
  end
  else
  begin
    //VOLTAMOS A IMAGEM INICIAL DE SELEÇÃO PARA AS
    //DUAS OPÇÕES ESCOLHIDAS QUE NÃO SÃO IGUAIS
    ComponenteSelecionado1.Picture := img_0.Picture;
    ComponenteSelecionado2.Picture := img_0.Picture;
  end;

  //LIMPA AS VARIAVEIS DE COMPARAÇÃO PARA SELECIONAR MAIS DUAS
  ImagemSelecionada1 := '';
  ImagemSelecionada2 := '';

  //SOMA UMA NOVA JOGADA
  qtdeJogadas         := StrToInt(edtQtdJogadas.Text);
  qtdeJogadas         := qtdeJogadas + 1;
  edtQtdJogadas.Text := IntToStr(qtdeJogadas);

  //VERIFICA SE FINALIZOU O JOGO
  if (JogoTerminou) then
  begin
    tmContador.Enabled   := false;
    btnIniciar.Visible   := true;
    btnPausar.Visible    := false;
    btnRecomecar.Visible := false;

    RegistrarJogada;
  end;
end;

procedure TForm1.TmContadorTimer(Sender: TObject);
var
  tempo:TTime;
begin
    tempo:= strtotime (EdtTempoJogado.Text);
    tempo:= tempo + strtotime ('00:00:01');
    EdtTempoJogado.Text:= timetostr(tempo);

end;

end.
