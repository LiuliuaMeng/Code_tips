import java.sql.*;

public class MySqlConnector {
    private static final String ip = "10.123.123.123";

    private static final int port = 3306;
    private static final String username = "myname";
    private static final String password = "mypass";
    private static final String dbname = "mydbname";
    private static final String sql = "select * from mytable limit 10";

    public static void main(String[] args) {
        Connection con;
        //jdbc驱动
        String driver="com.mysql.jdbc.Driver";
        String url="jdbc:mysql://"+ip+":"+port+"/"+dbname;
        try {
            //注册JDBC驱动程序
            Class.forName(driver);
            //建立连接
            con = DriverManager.getConnection(url, username, password);
            if (!con.isClosed()) {

                System.out.println("数据库连接成功");
                PreparedStatement ps = con.prepareStatement(sql);
                ResultSet resultSet = ps.executeQuery();
                while(resultSet.next()){
                    System.out.println(resultSet.getString(1));
                }
                System.out.println("success");

            }
            con.close();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
            System.out.println("数据库驱动没有安装");
        } catch (SQLException e) {
            e.printStackTrace();
            System.out.println("数据库连接失败");
        } catch (Exception e) {
            e.printStackTrace();
            System.out.println("Exception 报错");
        }
    }


}


