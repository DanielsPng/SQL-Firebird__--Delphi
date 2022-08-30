# SQL-Firebird__&-Delphi

- HELLO WORLD!
    
    

---

- DELPHI
    
    # Botão Grava Cadastro (Delphi)
    
    - ——
        
        ```sql
        private
        codUsuario : Integer;
        ```
        
        ```sql
        Procedure TFrmCadastroUsuario.GravaCadastro();
        begin
        if NOT(TR_CADASTRO.Active) then
        TR_CADASTRO.Active := TRUE;
        try
        SP_CADASTRO.ParamByName('USUARIO_ID').AsInteger := codUsuario + 1;
        SP_CADASTRO.ParamByName('NOME').AsString := Edit1.text;
        SP_CADASTRO.ParamByName('SENHA').AsString := Edit2.text;
        SP_CADASTRO.ParamByName('ENDERECO').AsString := Edit3.text;
        SP_CADASTRO.ParamByName('ESTADO').AsString := Edit6.text;
        SP_CADASTRO.ParamByName('CID_CODIGO').AsInteger := CID_CODIGO.KeyValue;
        SP_CADASTRO.ParamByName('CELULAR').AsString := Edit8.text;
        SP_CADASTRO.ParamByName('EMAIL').AsString := Edit7.text;
        SP_CADASTRO.ParamByName('DATA_NASCI').AsDATE := USU_DATA_NASCIMENTO.DATE;
        SP_CADASTRO.ParamByName('CPF').AsString := Edit10.text;
        SP_CADASTRO.ParamByName('RG').AsString := Edit11.text;
        SP_CADASTRO.ExecProc;
        except on ERRO:exception do
        begin
        TR_CADASTRO.Rollback;
        ShowMessage('ERRO: '+Erro.Message + ' ERRO AO TENTAR GRAVAR CADASTRO!"');
        Abort;
        end;
        end;
        TR_CADASTRO.Commit;
        ATUALIZA_QRY();
        end;
        ```
        
    
    # Botão Excluir Cadastro (Delphi)
    
    - ———
        
        ```sql
        procedure TFrmCadastroUsuario.excluirRegistro(codigoUsuario:Integer);
        begin
        if NOT(TR_CADASTRO.Active) then
        TR_CADASTRO.Active := TRUE;
        try
        SP_CADASTRO.ParamByName('USUARIO_ID').AsInteger := codigoUsuario;
        SP_CADASTRO.ParamByName('NOME').AsString := '-1';
        SP_CADASTRO.ParamByName('SENHA').AsString := '';
        SP_CADASTRO.ParamByName('ENDERECO').AsString := '';
        SP_CADASTRO.ParamByName('ESTADO').AsString := '';
        SP_CADASTRO.ParamByName('CID_CODIGO').AsInteger := 0;
        SP_CADASTRO.ParamByName('CELULAR').AsString := '';
        SP_CADASTRO.ParamByName('EMAIL').AsString := '';
        SP_CADASTRO.ParamByName('DATA_NASCI').AsDate := Date;
        SP_CADASTRO.ParamByName('CPF').AsString := '';
        SP_CADASTRO.ParamByName('RG').AsString := '';
        SP_CADASTRO.ExecProc;
        except on ERRO:exception do
        begin
        TR_CADASTRO.Rollback;
        ShowMessage('ERRO: '+Erro.Message + 'ERRO AO TENTAR EXCLUIR CADASTRO!"');
        Abort;
        end;
        end;
        TR_CADASTRO.Commit;
        ATUALIZA_QRY();
        end;
        ```
        
        ```sql
        procedure TFrmCadastroUsuario.ExcluirClick(Sender: TObject);
        begin
        excluirRegistro(DBGrid1.DataSource.DataSet.FieldByName('USUARIO_ID').AsInteger);
        end;
        ```
        
    
    # FormKeyDown
    
    - ————
        
        ```sql
        procedure TFrmCadastroUsuario.FormKeyDown(Sender: TObject; var Key: Word;
        Shift: TShiftState);
        begin
        if (key = vk_f1) then
        begin
        abrecadastrocidade();
        end;
        if (key = vk_f2) then
        begin
        abrecadastroInstrumentos();
        end;
        end;
        ```
        
        ```sql
        procedure TFrmCadastroUsuario.abrecadastrocidade;
        begin
        Application.CreateForm(TFrmCadastroCidade, FrmCadastroCidade);
        try
        FrmCadastroCidade.ShowModal;
        finally
        FreeAndNil(FrmCadastroCidade);
        end;
        end;
        ```
        
    
    # Busca ID
    
    - —————
        
        ```sql
        procedure TFrmCadastroUsuario.BUSCA_ID_USUARIO();
        begin
        QRY_BUSCAID.CLOSE;
        QRY_BUSCAID.SQL.CLEAR;
        QRY_BUSCAID.SQL.ADD('SELECT FIRST 1 USUARIO_ID, NOME FROM USUARIO');
        QRY_BUSCAID.SQL.ADD('ORDER BY USUARIO_ID DESC');
        QRY_BUSCAID.OPEN;
        ```
        
        ```sql
        codUsuario := QRY_BUSCAID.FieldByName('usuario_ID').AsInteger;
        end;
        ```
        
    
    # Botão enviar
    
    - ——————
        
        ```sql
        procedure TFrmCadastroUsuario.BtnGravarClick(Sender: TObject);
        begin
        BUSCA_ID_USUARIO();
        GravaCadastro();
        limpatexto();
        end;
        ```
        
    
    # Limpa Texto
    
    - ———————-
        
        ```sql
        procedure TFrmCadastroUsuario.limpatexto();
        var
        n:integer;
        
        begin
        
        for n := 0 to ComponentCount -1 do begin
        
        if Components[n] is Tedit then
        
        Tedit(Components[n]).Text := '';
        
        end;
        end;
        ```
        
    
    # Dbl Click
    
    - ————————-
        
        ```sql
        procedure TFrmCadastroCidade.DBGrid1DblClick(Sender: TObject);
        begin
        EDIT_CIDADE.Text :=  DBGrid1.DataSource.DataSet.FieldByName('CID_DESCRICAO').AsString;
        Edit1.Text :=  IntToStr(DBGrid1.DataSource.DataSet.FieldByName ('CID_COD_IBGE').AsInteger);
        Edit2.Text :=  DBGrid1.DataSource.DataSet.FieldByName ('CID_UF').AsString;
        Edit3.Text :=  DBGrid1.DataSource.DataSet.FieldByName ('CID_PAIS').AsString;
         AUX_CODIGO_CIDADE := IntToStr(DBGrid1.DataSource.DataSet.FieldByName ('CID_codigo').AsInteger);
         editar := True;
        end;
        ```
        
    
    # Atualiza QRY
    
    - ——————————
        
        ```sql
        procedure ATUALIZAQRY(NOME_QRY: TIBQuery);
        ```
        
        ```sql
        procedure TFrmCadastroCidade.ATUALIZAQRY(NOME_QRY: TIBQuery);
        begin
        NOME_QRY.Close();
        NOME_QRY.OPEN();
        end;
        ```
        
    
    # Shapes
    
    - ————————————
        
        No delphi é possível alterar os shapes usando o tamanho da borda, caso esteja usando o retângulo redondo, quanto mais aumentar a borda, mais o shape vai arredondar.
        

---

# EXEMPLOS DE CODIGO SQL - FIREBIRD

- SELECT - INSERT - UPDATE - DELETE - WHERE
    - > SELECT (BUSCA REGISTROS).
    
    + EXEMPLO BASE: 
    
    ```sql
    SELECT * FROM TABLE.
    ```
    
     * INDICA QUE ESTA BUSCANDO TODOS OS CAMPOS DO REGISTRO.
    
    	--> FROM: INDICA QUE A BUSCA SERA REALIZA A PARTIR DE ALGO, NO CASO UMA TABELA.
    
    	--> TABLE: DEVE SER INFORMADO O NOME DA TABELA.
    
    + EXEMPLO DE CASO
    	--> IRA BUSCAS O CODIGO DE TODAS AS EMPRESAS DA TABELA EMPRESA.
    
    ```sql
    SELECT EMP_CODIGO FROM EMPRESA
    ```
    
    - > INSERT (GRAVA O REGISTRO).
    
    ```
    + EXEMPLO BASE: INSERT INTO () VALUES ().
    
    + EXEMPLO DE CASO: INSERT INTRO EMPRESA (EMP_CODIGO, EMP_NOME) VALUE (1,'SIMBOLUS').
    	--> IRA INSERIR UM NOVO REGISTRO NA TABELA, SENDO EMPRESA 1 NOMEADA SIMBOLUS.
    
    ```
    
    - > UPDATE (ATUALIZA A INFORMAÇÃO DO REGISTRO).
    
    ```
    + EXEMPLO BASE: UPDATE TABLE SET 'CAMPO A SER ALTERADO' = 'VALO DE ALTERAÇÃO'
    
    + EXEMPLO DE CASO: UPDATE EMPRESA SET EMP_CODIGO = 'SOFTHOUSE'
    	--> IRA ATUALIZAR O NOME DE TODAS AS EMPRESAS PARA SOFTHOUSE, DETRO DA TABELA EMPRESA
    
    ```
    
    - > DELETE (DELETA REGISTROS).
    
    ```
    + EXEMPLO BASE: DELETE FROM TABLE
    
    + EXEMPLO DE CASO: DELETE FROM EMPRESA
    	--> IRA DELETAR TODOS OS REGISTROS DA TABELA EMPRESA.
    
    ```
    
    - > WHERE (CONDIÇÃO LOGICA PARA IDENTIFICAR OS REGISTROS QUE DESEJA MANIPULAR)
    
    ```
    + EXEMPLO (SELECT): SELECT PRO_DESCRICAO FROM PRODUTO WHERE PRO_CODIGO = 1
    	--> IRA RETORNAR A DESCRICAO DO PRODUTO, SENDO APENAS DO QUE TIVER CODIGO IGUAL A 1
    
    + EXEMPLO (UPDATE): UPDATE PRODUTO SET PRO_DESCRICAO = 'CAFÉ' WHERE PRO_CODIGO <= 10
    	--> IRA ALTERAR A DESCRIÇÃO DE TODOS OS PRODUTOS COM CODIGO MENOR OU IGUAL A 10
    
    + EXEMPLO (DELETE): DELETE FROM PRODUTO WHERE PRO_CODIGO >= 15
    	--> IRA DELETAR TODOS OS PRODUTOS COM O CODIGO MAIOR OU IGUAL A 15
    
    ```
    
    - ---------------------> EXERCICIOS.
    
    ```
    1.  A. REALIZAR BUSCA DE TODOS OS CLIENTES CADASTRADOS ANTES DE 2022.
    
    2.  A. REALIZAR BUSCA DE TODOS OS CLIENTES DA CIDADE DE 'DOIS CORREGOS'.
    
    3.  A. REALIZAR A BUSCA DE TODAS AS CIDADE QUE FAZEM PARTE DO ESTADO DE SÃO PAULO
    	B. ALTERAR O NOME(DESCRICAO) DE TODAS AS CIDADES DO ESTADO DE SÃO PAULO PARA 'BRASILEIRO'
    	C. DELETAR TODAS AS CIDADE DO ESTADO DE GOIAS
    	D. INSERIR UM NOVO REGISTRO:
    		+ CODIGO DA CIDADE (VERIFICAR O ULTIMO REGISTRO, NÃO PODE CRIAR COM MESMO CODIGO)
    		+ DESCRIÇÃO: 'CIDADE AGRO'
    		+ ESTADO: 'VOCÊ ESCOLHE'
    		+ CID_CODIGO_IBGE: 'VOCÊ ESCOLHE'
    		+ PAI_CODIGO: 1058
    
    4.  A. REALIZAR A BUSCA DE PRODUTOS COM VALOR MENOR A R$ 50,00.
    	B. ALTERAR O NOME DE TODOS OS PRODUTOS COM VALOR MENOR A R$ 25,00 PARA 'PICOLE'.
    	C. EXCLUIR TODOS OS PRODUTOS COM VALOR ACIMA DE R$ 300,00.
    	D. INSERIR UM NOVO PRODUTO.
    
    + VERIFICA O ULTIMO CODIGO LANÇADO NAS TABELAS, VOCÊ NÃO PODE CRIAR REGISTROS IGUAIS.
    
    ```
    
    - ---------------------> LINKS DE APOIO.
    
    FUNÇÕES BASE SQL:
    [https://www.devmedia.com.br/comandos-basicos-em-sql-insert-update-delete-e-select/37170#3](https://www.devmedia.com.br/comandos-basicos-em-sql-insert-update-delete-e-select/37170#3)
    
    OPERADORES LOGICOS UTILIZADOS NA CONDIÇÃO WHERE:
    [https://www.devmedia.com.br/usando-os-operadores-like-in-e-between-no-oracle/24687](https://www.devmedia.com.br/usando-os-operadores-like-in-e-between-no-oracle/24687)
    
    ---
    
- CHAVE PRIMARIA E CHAVE ESTRANGEIRA
    
    ```sql
    CREATE TABLE CANTORES (
    id   INTEGER PRIMARY KEY,
    Nome_do_cantor  TEXT);
    
    CREATE TABLE MUSICAS (
    Nome_da_muisica   TEXT,
    CANTOR            INTEGER,
    FOREIGN KEY(CANTOR) REFERENCES CANTORES(id));
    
    INSERT INTO CANTORES VALUES (1, 'Michel Jackson');
    INSERT INTO CANTORES VALUES (2, 'Freddie Mercury');
    
    INSERT INTO musicas VALUES ( 'Billie Jean', 1);
    INSERT INTO musicas VALUES ( 'Bad', 1);
    INSERT INTO musicas VALUES ( 'Somebody to Love', 2);
    
    ```
    
- INNER JOIN
    
    ```sql
    SELECT PR.PRO_DESCRICAO, FR.FOR_NOME
    FROM PRODUTO PR
    INNER JOIN FORNEC_MATERIAIS FM ON PR.PRO_CODIGO = FM.PRO_CODIGO
    INNER JOIN FORNECEDOR FR ON FM.FOR_CODIGO = FR.FOR_CODIGO
    ORDER BY PR.PRO_DESCRICAO
    ```
    
    ```sql
    SELECT US.*, CD.CID_DESCRICAO
    FROM USUARIO US
    INNER JOIN CIDADE CD ON US.CID_CODIGO = CD.CID_CODIGO
    
    ```
    
    ```sql
    SELECT AU.*, PR.PRO_NOME, IT.INS_DESCRICAO
    FROM AULAS AU
    INNER JOIN PROFESSORES PR ON AU.PRO_CODIGO = PR.PRO_CODIGO
    INNER JOIN INSTRUMENTOS IT ON AU.INS_CODIGO = IT.INS_CODIGO
    ```
    
    - Cliente Razão - Cidade - Estado - Cadastro
        
        ```sql
        SELECT CL.CLI_RAZAO, CD.CID_DESCRICAO, CLI_CADASTRO, CD.CID_UF
        FROM CLIENTE CL
        INNER JOIN CIDADE CD ON CL.CLI_CODIGO = CD.CID_CODIGO
        
        WHERE CD.CID_UF = 'SP'
        ```
        
- MATH
    - COUNT
        
        ```sql
        SELECT COUNT(*) AS BANCOS FROM BANCO
        ```
        
        ---
        
        ---
        
        ```sql
        SELECT COUNT(*) AS BANCOS
        FROM BANCO
        WHERE BAN_CODIGO > 1
        ```
        
        ---
        
        ---
        
        ```sql
        SELECT COUNT(*) AS CLIENTE_REGISTRADOS
        FROM CLIENTE
        WHERE CLI_CADASTRO < '01/01/2016'
        AND CLI_CODIGO > 200
        ```
        
    - MAX & MIN
        
        ```sql
        SELECT
        MAX(preco) as MAIOR_PRECO
        FROM
        produto
        ```
        
        > Trará o valor máximo da coluna preço através da função MAX
        —————--mínimo-——————————————— MIN
        > 
        
    - AVG
        
        ```sql
        SELECT
        AVG(preco) as PRECO_MEDIO
        FROM
        produto
        ```
        
        > Traz a média de valores de uma determinada coluna informada na consulta.
        > 
        

## Exercício

- Todos os produtos referente aos itens das NFs, para os fornecedores que possuem cadastro fora do Estado de SP
    
    ```sql
    SELECT  NF.VEN_NF, FR.FOR_NOME, CD.CID_DESCRICAO, CD.CID_UF, NF.NFI_COD_PRODUTO, PR.PRO_DESCRICAO, NF.NFI_QTDE, NF.NFI_VALOR
    FROM PRODUTO PR
    INNER JOIN NF_ITENS NF ON PR.PRO_CODIGO = NF.VEN_NF
    INNER JOIN FORNECEDOR FR ON NF.VEN_NF = FOR_CODIGO
    INNER JOIN CIDADE CD ON FOR_CODIGO = CID_CODIGO
    
    WHERE CD.CID_UF <> 'SP'
    ```
    
    ```sql
    SELECT  NF.NFI_CODIGO, FR.FOR_NOME, CD.CID_DESCRICAO, CD.CID_UF, NF.NFI_COD_PRODUTO, PR.PRO_DESCRICAO, SUM(NF.NFI_QTDE * NF.NFI_VALOR) AS TOTAL
    FROM PRODUTO PR
    INNER JOIN NF_ITENS NF ON PR.PRO_CODIGO = NF.VEN_NF
    INNER JOIN FORNECEDOR FR ON NF.VEN_NF = FOR_CODIGO
    INNER JOIN CIDADE CD ON FOR_CODIGO = CID_CODIGO
    
    WHERE CD.CID_UF <> 'SP'
    GROUP BY 1, 2, 3, 4, 5, 6
    
    ORDER BY NFI_CODIGO
    ```
    

# TABLE

```sql
CREATE TABLE USUARIO (
USUARIO_ID INT NOT NULL PRIMARY KEY,
NOME VARCHAR (255),
CIDADE VARCHAR (255),
ESTADO VARCHAR (255),
CELULAR VARCHAR (255),
SENHA VARCHAR (255)
);
```

# Exemplo (Stored Procedure):

- —————
    
    ```sql
    
    COMMIT WORK;
    SET AUTODDL OFF;
    SET TERM ^ ;
    
    /* Stored procedures */
    
    CREATE OR ALTER PROCEDURE SP_CADASTRO_ALUNOS
    (
      ALU_CODIGO INTEGER,
    	ALU_NOME VARCHAR (255),
    	ALU_CPF INTEGER,
    	ALU_DATA_NASCI DATE
    )
    AS
    BEGIN
    IF (EXISTS(SELECT ALU_CODIGO FROM ALUNOS WHERE ALU_CODIGO = :ALU_CODIGO)) THEN
       BEGIN
       IF (ALU_NOME = '-1') THEN
          DELETE FROM ALUNOS WHERE ALU_CODIGO = :ALU_CODIGO;
       ELSE
          UPDATE ALUNOS SET ALU_NOME = :ALU_NOME, ALU_CPF = :ALU_CPF, ALU_DATA_NASCI = :ALU_DATA_NASCI
          WHERE ALU_CODIGO = :ALU_CODIGO;
       END
    ELSE
       INSERT INTO ALUNOS (ALU_CODIGO, ALU_NOME, ALU_CPF, ALU_DATA_NASCI)
       VALUES(:ALU_CODIGO, :ALU_NOME, :ALU_CPF, :ALU_DATA_NASCI);
    END
    ^
    
    SET TERM ; ^
    COMMIT WORK;
    SET AUTODDL ON;
    ```
    
    ```sql
    COMMIT WORK;
    SET AUTODDL OFF;
    SET TERM ^ ;
    
    /* Stored procedures */
    
    CREATE OR ALTER PROCEDURE SP_CADASTRO_AULAS
    (
      AULA_CODIGO INTEGER,
      AULA_MATERIA VARCHAR (255),
      AULA_DIA VARCHAR (255),
      AULA_HORARIO VARCHAR (10),
      AULA_DURAÇÃO VARCHAR (10),
      ALU_CODIGO INTEGER,
      PRO_CODIGO INTEGER,
      INS_CODIGO INTEGER
    )
    AS
    BEGIN
    IF (EXISTS(SELECT AULA_CODIGO FROM AULAS WHERE AULA_CODIGO = :AULA_CODIGO)) THEN
       BEGIN
       IF (AULA_MATERIA = '-1') THEN
          DELETE FROM AULAS WHERE AULA_CODIGO = :AULA_CODIGO;
       ELSE
          UPDATE AULAS SET AULA_MATERIA = :AULA_MATERIA, AULA_DIA = :AULA_DIA, AULA_HORARIO = :AULA_HORARIO, AULA_DURAÇÃO = :AULA_DURAÇÃO,
          ALU_CODIGO = :ALU_CODIGO, PRO_CODIGO = :PRO_CODIGO, INS_CODIGO = :INS_CODIGO
          WHERE AULA_CODIGO = :AULA_CODIGO;
       END
    ELSE
       INSERT INTO AULAS (AULA_CODIGO, AULA_MATERIA, AULA_DIA, AULA_HORARIO, AULA_DURAÇÃO, ALU_CODIGO, PRO_CODIGO, INS_CODIGO)
       VALUES(:AULA_CODIGO, :AULA_MATERIA, :AULA_DIA, :AULA_HORARIO, :AULA_DURAÇÃO, :ALU_CODIGO, :PRO_CODIGO, :INS_CODIGO);
    END
    ^
    
    SET TERM ; ^
    COMMIT WORK;
    SET AUTODDL ON;
    ```
    
    ```sql
    COMMIT WORK;
    SET AUTODDL OFF;
    SET TERM ^ ;
    
    /* Stored procedures */
    
    CREATE OR ALTER PROCEDURE "SP_CADASTRO_AA" 
    (
      "AA_CODIGO" INTEGER,
      "ALU_CODIGO" INTEGER,
      "AULA_CODIGO" INTEGER
    )
    RETURNS
    (
      "CODIGO" INTEGER
    )
    AS
    BEGIN
    IF (EXISTS(SELECT AA_CODIGO FROM ALUNOS_AULAS WHERE AA_CODIGO = :AA_CODIGO)) THEN
       BEGIN
       IF (ALU_CODIGO = '-1') THEN
          DELETE FROM ALUNOS_AULAS WHERE AA_CODIGO = :AA_CODIGO;
       ELSE
          UPDATE ALUNOS_AULAS SET ALU_CODIGO = :ALU_CODIGO, AULA_CODIGO = :AULA_CODIGO
          WHERE AA_CODIGO = :AA_CODIGO;
       END
    ELSE
       CODIGO = GEN_ID(GEN_COD_AA,1);
       INSERT INTO ALUNOS_AULAS (AA_CODIGO, ALU_CODIGO, AULA_CODIGO)
       VALUES(:CODIGO, :ALU_CODIGO, :AULA_CODIGO);
    END
     ^
    
    SET TERM ; ^
    COMMIT WORK;
    SET AUTODDL ON;
    ```
    
    ```sql
    COMMIT WORK;
    SET AUTODDL OFF;
    SET TERM ^ ;
    
    /* Stored procedures */
    
    CREATE OR ALTER PROCEDURE "SP_CADASTRO_USUARIO" 
    (
      "USU_CODIGO" INTEGER,
      "USU_DESCRICAO" VARCHAR(255),
      "USU_SENHA" VARCHAR(255)
    )
    AS
    BEGIN
    IF (EXISTS(SELECT USU_CODIGO FROM USUARIOS WHERE USU_CODIGO = :USU_CODIGO)) THEN
       BEGIN
       IF (USU_DESCRICAO = '-1') THEN
          DELETE FROM USUARIOS WHERE USU_CODIGO = :USU_CODIGO;
       ELSE
          UPDATE USUARIOS SET USU_DESCRICAO = :USU_DESCRICAO, USU_SENHA = :USU_SENHA
          WHERE USU_CODIGO = :USU_CODIGO;
       END
    ELSE
       INSERT INTO USUARIOS (USU_CODIGO, USU_DESCRICAO, USU_SENHA)
       VALUES(:USU_CODIGO, :USU_DESCRICAO, :USU_SENHA);
    END
     ^
    
    SET TERM ; ^
    COMMIT WORK;
    SET AUTODDL ON;
    ```
    

# FOREIGN KEY (Chave Estrangeira)

- —————
    
    ```sql
    ALTER TABLE NOME_TABELA_QUE_FICA_CHAVE ADD CONSTRAINT FK_NOME_DA_SUA_CHAVE FOREIGN KEY (CODIGO)
    REFERENCES NOME_OUTRA_TABELA(CODIGO);
    ```
    
    ### EXEMPLO
    
    ```sql
    ALTER TABLE AULAS ADD CONSTRAINT FK_AULAS_INSTRUMENTOS FOREIGN KEY (INS_CODIGO)
    REFERENCES INSTRUMENTOS(INS_CODIGO);
    ```
    

# DROP

- EXEMPLOS
    
    ```sql
    DROP TABLE *table_name*
    ```
    
    ```sql
    ALTER TABLE table_name
    DROP COLUMN column_name
    ```
    

# New Project

- ——————-
    
    ```sql
    CREATE TABLE AULAS (
    AULA_CODIGO INT NOT NULL PRIMARY KEY,
    AULA_MATERIA VARCHAR (255),
    AULA_DIA VARCHAR (255),
    AULA_HORARIO VARCHAR (10),
    AULA_DURACAO VARCHAR (10),
    ALU_CODIGO INTEGER,
    PRO_CODIGO INTEGER,
    INS_CODIGO INTEGER
    ); 
    ```
    
    ```sql
    CREATE TABLE ALUNOS (
    ALU_CODIGO INT NOT NULL PRIMARY KEY,
    ALU_NOME VARCHAR (255),
    ALU_CPF INTEGER,
    ALU_DATA_NASCI DATE
    );
    ```
    
    ```sql
    CREATE TABLE PROFESSORES (
    PRO_CODIGO INT NOT NULL PRIMARY KEY,
    PRO_NOME VARCHAR (255),
    PRO_CPF INTEGER,
    PRO_DATA_NASCI DATE
    );
    ```
    
    ```sql
    CREATE TABLE INSTRUMENTOS (
      INS_CODIGO	 INTEGER NOT NULL PRIMARY KEY,
      INS_DESCRICAO	 VARCHAR(255),
      INS_TIPO	 VARCHAR(255),
      INS_AFINACAO	 VARCHAR(255) 
    );
    ```
    
    Criar USUÁRIO
    
    linkar professores no usuário para que eles consigam gerenciar
    
    ```sql
    CREATE TABLE USUARIOS (
      USU_CODIGO	INTEGER NOT NULL PRIMARY KEY,
      USU_DESCRICAO VARCHAR(255),
      USU_SENHA	 VARCHAR(255),
    	PRO_CODIGO INTEGER
    );
    ```
    
    ```sql
    SELECT AU.AULA_CODIGO, AU.AULA_DIA, AU.AULA_HORARIO, AU.AULA_DURACAO, AU.AULA_MATERIA, AA.ALU_CODIGO, AL.ALU_NOME, PR.PRO_CODIGO, PR.PRO_NOME, INS.INS_CODIGO, INS.INS_DESCRICAO
    FROM AULAS AU
    INNER JOIN INSTRUMENTOS INS ON AU.INS_CODIGO = INS.INS_CODIGO
    INNER JOIN PROFESSORES PR ON AU.PRO_CODIGO = PR.PRO_CODIGO
    INNER JOIN ALUNOS_AULAS AA ON AU.AULA_CODIGO = AA.AULA_CODIGO
    INNER JOIN ALUNOS AL ON AA.ALU_CODIGO = AL.ALU_CODIGO
    GROUP BY 1,2,3,4,5,6,7,8,9,10,11
    ORDER BY AU.AULA_CODIGO
    ```
    
    ```sql
    Application.CreateForm(TFRelListagemUsuarios, FRelListagemUsuarios);
    Try
    FRelListagemUsuarios.QuickRep1.PreviewModal;
    Finally
    FreeAndnil(FRelListagemUsuarios);
    End;
    ```
    

```sql
//GetBitMap(<index do imagelist>,<botão que irá receber o ícone>)
FrmPrincipal.ImageList1.GetBitmap(9,SpdBtnOpenNFVen.Glyph);
FormCreate
```

Alteração: aumento da dimensão do menu principal, ícones atualizados no menu principal, imagem de círculos meramente ilustrativos no canto inferior esquerdo, logo Simbolus atualizado no lado direito

HEX : #324372  - cor azul

Button to frm. ToolButton

```sql
procedure TFrmPrincipal.ActClienteExecute(Sender: TObject);
begin
   Application.CreateForm(TFrmClientes2, FrmClientes2);
   Try
      if ToolButton1.Hint = 'CADASTRAR-Consultar Clientes' then
         FrmClientes2.Consulta_Dados := true

      FrmClientes2.ShowModal;
   Finally
      FreeAndnil(FrmClientes2);
   End;
end;
```

IMG Button

```sql
procedure TFrmPrincipal.img_nfeClick(Sender: TObject);
begin
Application.CreateForm(TFrmNFAvulsa, FrmNFAvulsa);
FrmNFAvulsa.ShowModal;
end;
```

```sql
procedure TFrmPrincipal.img_nfeMouseEnter(Sender: TObject);
begin
(Sender as TImage).Top    := (Sender as TImage).Top - 4;
end;
```

```sql
procedure TFrmPrincipal.img_nfeMouseLeave(Sender: TObject);
begin
(Sender as TImage).Top    := (Sender as TImage).Top + 4;
end;
```
