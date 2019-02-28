# mysql-docker
Simples conexão usando MySql + Java JDBC + Docker


```java
/**
 *  
 *  Comandos para executar e testar o MySql no docker
 * 
 *  docker pull mysql
 *  
 * docker run --name bd_sql_jpa mysql -e MYSQL_ROOT_PASSWORD=terra -p 3306:3306 -d mysqldocker
 *
 * docker exec -it bd_sql_jpa mysql -p ( vai pedir a senha, digitar "terra" )
 *
 *
 */



import java.sql.*;
import java.text.SimpleDateFormat;

public class Conexao {

	private Connection con;

	public static void main(String[] args) {
		new Conexao().executar();
	}

	public void executar() {
		try {
			criar();
			selecionar();
		} catch (ClassNotFoundException e) {
			System.out.println(e.getMessage());
		} catch (SQLException e) {
			System.out.println(e.getMessage());
		}

	}

	private void conexao() throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.cj.jdbc.Driver");
		con = (Connection) DriverManager.getConnection("jdbc:mysql://localhost:3306/teste", "root", "terra");
	}

	private void criar() throws ClassNotFoundException, SQLException {
		conexao();

		// Criar a tabela
		Statement stm = (Statement) con.createStatement();
		stm.executeUpdate("CREATE TABLE funcionario (" + " matricula INT NOT NULL, " + " nome VARCHAR(50),"
				+ " PRIMARY KEY (matricula))");
		stm.close();

		// Adicionar 100 funcionarios
		PreparedStatement pstm = con.prepareStatement("INSERT INTO funcionario (matricula, nome) VALUES (?, ?)");
		int tot = 0;
		for (int i = 0; i < 100; i++) {
			pstm.setInt(1, i);
			pstm.setString(2, "Funcionário " + i);
			tot += pstm.executeUpdate();
		}

		System.out.println("Inseridos : " + tot);
		pstm.close();
		con.close();
	}

	private void selecionar() throws ClassNotFoundException, SQLException {
		SimpleDateFormat fdt = new SimpleDateFormat("hh:mm:ss:SSSS");
		conexao();

		// Varrer todos os dados
		Statement stm = (Statement) con.createStatement();

		ResultSet res = stm.executeQuery("SELECT nome, matricula FROM funcionario");

		int x = 0;
		String nome = null;
		int matricula;
		System.out.println("Tempo Inicial: " + fdt.format(new java.util.Date()));
		while (res.next()) {
			nome = res.getString(1);
			matricula = res.getInt(2);
			x += 1;
		}
		System.out.println("Tempo Final: " + fdt.format(new java.util.Date()));
		System.out.println("O último que li foi " + nome);
		System.out.println("Total de " + x);
	}
}

```
