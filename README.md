<%@ page contentType="text/html; charset=GB2312"%>
<%@ page import="java.sql.*" %>
<%@ page import="java.io.*" %>
<html>
<body>

//在原本代码段前新增一段代码，初始化total值
<%
//创建本地文件
  //写文件
int total=0;
   String filename = request.getRealPath("lionsky.txt");
 java.io.File f = new java.io.File(filename);
       if(!f.exists())//如果文件不存，则建立
  {
     f.createNewFile();

   }
else{//如果文件存在则读取
java.io.FileReader fr = new java.io.FileReader(f);
BufferedReader br = new BufferedReader(fr);
//int length;
//读文件内容
//out.write(filename+"<br>");
String str1 = br.readLine();
fr.close();
int  a = Integer.parseInt(str1.trim());
total=a;//将文件内容赋值给total
}
%>

//原本此处初始化total值代码段被删除


<%
boolean vote=true;
String name="";
name=request.getParameter("name");
if(name==null)
	{name="?";}
byte a[]=name.getBytes("ISO-8859-1");
name=new String(a);
String IP=(String)request.getRemoteAddr();
try{Class.forName("sun.jdbc.odbc.JdbcOdbcDriver");
}
catch(ClassNotFoundException e){ }
Connection con=null;
Statement sql=null;
ResultSet rs=null;

try{ con=DriverManager.getConnection("jdbc:odbc:vote", "", "");
sql=con.createStatement();
rs=sql.executeQuery("SELECT * FROM IP WHERE IP="+"'"+IP+"'");
int row=0;
while(rs.next())
{row++;
}
if(row>=1)
{vote=false;}
}
catch(SQLException e){ }
if(name.equals("?"))
{out.print("你没投票，不能看选举结果");}
else
{
	if(vote){
	out.print("您投了一票");
total++;
//将total值记录到本地文件
try
 {
   PrintWriter pw = new PrintWriter(new FileOutputStream(filename));
   pw.println(total);//写内容
  pw.close();
 } catch(IOException e) {
   out.println(e.getMessage());
 }

	try
	{rs=sql.executeQuery("SELECT * From people WHERE name="+"'"+name+"'");
	rs.next();
	int count=rs.getInt("count");
	count++;
	String condition="UPDATE people SET count="+count+" WHERE name="+"'"+name+"'";
	sql.executeUpdate(condition);
	String to="INSERT INTO IP VALUES "+"("+"'"+IP+"'"+")";
	sql.executeUpdate(to);
	}
	catch(SQLException e)
	{out.print(""+e);
	}
	try
	{rs=sql.executeQuery("SELECT * FROM people ");
	out.print("<Table Border>");
	out.print("<TR>");
	out.print("<TH width=100>"+"姓名");
	out.print("<TH width=50>"+"得票数");
	out.print("<TH width=50>"+"总票数: "+total);
	out.print("</TR>");
	while(rs.next())
	{out.print("<TR>");
	out.print("<TD>"+rs.getString(1)+"</TD>");
	int count=rs.getInt("count");
	out.print("<TD>"+count+"</TD>");
	double b=(count*100)/total;
	out.print("<TD>"+b+"%"+"</TD>");
	out.print("</TR>");
	}
	out.print("</Table>");
	con.close();
	}
	catch(SQLException e){ }
}
else
{out.print("你已经投过票了");
}

}

%>
</body>
</html>
