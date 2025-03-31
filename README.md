
# Manual de SQL e Conexão com PostgreSQL em Java

## 1. Conceitos Básicos

### Campo, Tabela, Linha, Coluna

- **Campo**: Um campo é uma unidade de dados em um banco de dados, geralmente corresponde a um tipo específico de dado (ex: `int`, `varchar`, etc.).
- **Tabela**: Uma tabela é uma coleção de dados organizados em linhas e colunas. Cada tabela deve ter um nome único em um banco de dados.
- **Linha**: Uma linha em uma tabela é uma coleção de dados de um único registro. Cada linha contém valores para as colunas de uma tabela.
- **Coluna**: Uma coluna representa um campo de dados em uma tabela. Cada coluna tem um tipo de dado associado.

## 2. Tipos de Dados no Banco de Dados

- **INT**: Tipo numérico para valores inteiros.
- **VARCHAR(n)**: Tipo de dado para strings de caracteres de comprimento variável.
- **TEXT**: Usado para armazenar textos longos.
- **DATE**: Usado para armazenar datas (ano, mês, dia).
- **BOOLEAN**: Armazena valores lógicos (verdadeiro ou falso).
- **FLOAT/DOUBLE**: Usado para valores numéricos com casas decimais.

## 3. Conexão em Java com PostgreSQL

Para conectar o Java ao PostgreSQL, você precisará do **JDBC** (Java Database Connectivity). O driver JDBC para PostgreSQL pode ser adicionado ao arquivo `pom.xml` do Maven.

### Exemplo de dependência no arquivo `pom.xml`

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>42.2.5</version>
</dependency>
```

## 4. Comando de **CREATE DATABASE**, **DROP DATABASE**, **CREATE TABLE**, e **DROP TABLE**

### Criar um banco de dados

```sql
CREATE DATABASE nome_do_banco;
```

### Apagar um banco de dados

```sql
DROP DATABASE nome_do_banco;
```

### Criar uma tabela

```sql
CREATE TABLE clientes (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100),
    idade INT
);
```

### Apagar uma tabela

```sql
DROP TABLE clientes;
```

## 5. Execução dos Comandos no PostgreSQL com Java

Para executar comandos SQL usando Java, você pode usar a classe `Connection`, `Statement` ou `PreparedStatement`.

### Exemplo de Conexão e Execução de Comando no PostgreSQL:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

public class ExemploPostgreSQL {
    public static void main(String[] args) {
        Connection conn = null;
        Statement stmt = null;
        
        try {
            // Conectar ao banco de dados
            conn = DriverManager.getConnection("jdbc:postgresql://localhost:5432/seu_banco", "seu_usuario", "sua_senha");
            
            // Criar um statement
            stmt = conn.createStatement();
            
            // Executar um comando SQL
            String sql = "CREATE TABLE clientes (id SERIAL PRIMARY KEY, nome VARCHAR(100), email VARCHAR(100), idade INT)";
            stmt.executeUpdate(sql);
            
            System.out.println("Tabela criada com sucesso!");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 6. Comando de **TRUNCATE TABLE**

### Truncar uma tabela

```sql
TRUNCATE TABLE nome_da_tabela;
```

Este comando exclui todos os dados da tabela, mas mantém a estrutura da tabela.

## 7. Comando de **INSERT**

### Inserir dados em uma tabela

```sql
INSERT INTO clientes (nome, email, idade) VALUES ('João', 'joao@exemplo.com', 30);
```

## 8. Execução do Comando de **INSERT** no PostgreSQL com Java

### Exemplo de execução de **INSERT** no PostgreSQL com Java:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

public class InserirDados {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement pstmt = null;
        
        try {
            // Conectar ao banco de dados
            conn = DriverManager.getConnection("jdbc:postgresql://localhost:5432/seu_banco", "seu_usuario", "sua_senha");
            
            // Comando SQL para inserir dados
            String sql = "INSERT INTO clientes (nome, email, idade) VALUES (?, ?, ?)";
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, "Maria");
            pstmt.setString(2, "maria@exemplo.com");
            pstmt.setInt(3, 25);
            
            pstmt.executeUpdate();
            
            System.out.println("Dados inseridos com sucesso!");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (pstmt != null) pstmt.close();
                if (conn != null) conn.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 9. Comando de **SELECT**

### Selecionar dados de uma tabela

```sql
SELECT * FROM clientes;
```

### Selecionar dados com filtro

```sql
SELECT nome, email FROM clientes WHERE idade > 25;
```

## 10. Execução do Comando de **SELECT** no PostgreSQL com Java

### Exemplo de execução de **SELECT** no PostgreSQL com Java:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class ConsultarDados {
    public static void main(String[] args) {
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;
        
        try {
            // Conectar ao banco de dados
            conn = DriverManager.getConnection("jdbc:postgresql://localhost:5432/seu_banco", "seu_usuario", "sua_senha");
            
            // Criar um statement
            stmt = conn.createStatement();
            
            // Executar comando SELECT
            String sql = "SELECT * FROM clientes";
            rs = stmt.executeQuery(sql);
            
            // Exibir os dados no console
            while (rs.next()) {
                System.out.println("ID: " + rs.getInt("id"));
                System.out.println("Nome: " + rs.getString("nome"));
                System.out.println("Email: " + rs.getString("email"));
                System.out.println("Idade: " + rs.getInt("idade"));
                System.out.println("---");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (rs != null) rs.close();
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 11. Apresentação dos Dados no Console

Os dados retornados pelo comando `SELECT` podem ser exibidos no console através de um `ResultSet`. O exemplo acima já mostra como isso pode ser feito.

## 12. Exemplo de **JTable** no Java

Para apresentar os dados em uma interface gráfica, você pode usar o `JTable`:

```java
import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.sql.*;

public class ExemploJTable {
    public static void main(String[] args) {
        // Dados da tabela
        DefaultTableModel model = new DefaultTableModel();
        model.addColumn("ID");
        model.addColumn("Nome");
        model.addColumn("Email");
        model.addColumn("Idade");

        // Criar JTable
        JTable table = new JTable(model);
        JScrollPane scrollPane = new JScrollPane(table);

        // Criar janela para exibir a tabela
        JFrame frame = new JFrame("Tabela de Clientes");
        frame.add(scrollPane);
        frame.setSize(500, 300);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setVisible(true);

        // Conectar ao banco de dados e carregar os dados
        try (Connection conn = DriverManager.getConnection("jdbc:postgresql://localhost:5432/seu_banco", "seu_usuario", "sua_senha");
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery("SELECT * FROM clientes")) {
            
            // Adicionar os dados no JTable
            while (rs.next()) {
                model.addRow(new Object[] {
                    rs.getInt("id"),
                    rs.getString("nome"),
                    rs.getString("email"),
                    rs.getInt("idade")
                });
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

Este código cria uma janela com um `JTable` para exibir os dados dos clientes retirados do banco de dados.

---

Este manual cobre os comandos SQL e a integração com o Java, tanto para manipulação de dados quanto para exibição em uma interface gráfica. Caso precise de mais detalhes ou ajustes, estou à disposição para ajudar!

## 13. Exemplo de **Delete** no Java
```java
 try {
    int id = 1;
    String comando = "DELETE FROM veiculos WHERE id = ?";
    PreparedStatement pstmt;
    pstmt = conexao.prepareStatement(comando);
    pstmt.setInt(1,id);
    pstmt.executeUpdate();
    JOptionPane.showMessageDialog(this,"Apagado com Sucesso");
} catch (Exception e){
    JOptionPane.showMessageDialog(this,"Erro ao apagar");
}

```

## 14. Exemplo de **Deletar Tabela** no Java
```java

 try {
    String id_ = jLabel1.getText();
    int id = Integer.valueOf(id_);
    String comando = "DELETE FROM veiculos WHERE id = ?";
    PreparedStatement pstmt;
    pstmt = conexao.prepareStatement(comando);
    pstmt.setInt(1,id);
    pstmt.executeUpdate();
    JOptionPane.showMessageDialog(this,"Apagado com Sucesso");
    jButton6.doClick();
} catch (Exception e){
    JOptionPane.showMessageDialog(this,"Erro ao apagar");
}
```



