# 综合性实验  学生选课系统设计 

# 一、实验目的 
  分析学生选课系统 
  使用GUI窗体及其组件设计窗体界面 
  完成学生选课过程业务逻辑编程  
  基于文件保存并读取数据  
  处理异常 
  
 # 二、实验要求 
  1、系统角色分析及类设计 
  例如：学校有“人员”，分为“教师”和“学生”，教师教授“课程”，学生选择课程。 
  定义每种角色人员的属性，及其操作方法。 
  属性示例：	人员（编号、姓名、性别……） 
  教师（编号、姓名、性别、所授课程、……） 
        学生（编号、姓名、性别、所选课程、……） 
        课程（编号、课程名称、上课地点、时间、授课教师、……） 
  以上属性仅为示例，同学们可以自行扩展。  
  2、要求: 
    设计GUI窗体，支持学生注册、课程新加、学生选课、学生退课、打印学生选课列表等操作。 
    基于事件模型对业务逻辑编程，实现在界面上支持上述操作。 
    针对操作过程中可能会出现的各种异常，做异常处理 
    基于输入/输出编程，支持学生、课程、教师等数据的读写操作。 
    基于Github.com提交实验，包括实验SRC源文件夹程序、README.MD实验报告文档。 
    
  # 三、实验步骤  
     1、打开Eclipse--将项目Homework--建包cn.itcase_01--建类JDBCUTil、register、studentChooseCourse、userRegister
     2、userRegister为注册类，提供注册服务；register负责对用户的身份进行验证；工具类JDBCUtil用于实现连接，你可调用JDBCUtil的getConnection()方法等到链接；
        选课类studentChooseCourse用于，实现选课功能。其中包括几个主要方法：
          actionPerformed(ActionEvent) 用于监听用户“查询”和“提交”操作，并负责调用各种方法对其进行处理
          createSearchCourse()用于产生图形用户界面
          processBeforeCommit()用于对用户的“提交”查找进行验证，剔除无效的用户操作
          tryCommit()负责对有效的“提交”操作，进一步处理，并将有效的操作结果时时保存到数据库，并更新数据库原有信息 
          
 # 四、核心代码
        //对有效的提交操作的进行处理 
        public void tryCommit() { 
               //userInput为用户输入的课程ID. 
               String userInput = jtfSelectedCourse.getText().toString().trim().toLowerCase(); 
               //if course still available(count<MAX_STUDENT),save result. 
               //else if course not available,show Message to student. 
               PreparedStatement ps; 
               String sql = "select (Max-selectedCount) as RemainedCount " + "from Coursexx where cno=?"; 
               try { 
                    ps = con.prepareStatement(sql); 
       //取得学生ID或名字，将课程ID存入学生选课表choicesxx 
                    ps.setString(1, userInput); 
                    rs = ps.executeQuery(); 
                    ResultSetMetaData meta = rs.getMetaData(); 
                    int cols = meta.getColumnCount(); 
                    int RemainedCount = -1; 
              while (rs.next()) { 
                    RemainedCount = rs.getInt(1); 
                    System.out.println("RemainedCount:" + RemainedCount); 
              } 
      //如果该课程还有选择的名额，提示单项选课操作成功。 
          if (RemainedCount > 0) { 
              //save studentId and courseId to student-course table. 
              //this.getUsrName();userInput 
            sql = "insert into choicesxx values(?,?)"; 
            ps = con.prepareStatement(sql); 
            ps.setString(1, this.getUsrName()); 
            ps.setString(2, userInput); 
            ps.executeUpdate(); 
            JOptionPane.showMessageDialog(null, "选课成功： " + this.getUsrName() + " 选了" + userInput + "." + "" + " 还有 " 
                + RemainedCount + " 人可以选该课程。"); 
          //更新课程中已选该课程的人数：即将可选该课程的人数减去1个人。 
            sql = "update CourseXX set selectedCount=selectedCount+1 where cno=?"; 
            ps = con.prepareStatement(sql); 
            ps.setString(1, userInput); 
            ps.executeUpdate(); 
            con.commit(); 
      //如果该课程已经没有选择名额，提示重新选课 
          } 
        } catch (Exception es) { 
          es.printStackTrace(); 
          try { 
            con.rollback(); 
			} catch (Exception ey) { 
				ey.printStackTrace(); 
			} 
		} 
	}  
  
  //对用户查询课程信息，进行处理，并显示查询结果  
         	public void searchAvailableCourse() {  
//让程序自动选择连接的是Oracle或SqlServer.  
	         	if (JDBCUtil.getConnection() != null) {  
		              	System.out.println(JDBCUtil.getConnection());  
			               con = JDBCUtil.getConnection();  
		         } else { 
		   	con = JDBCUtil.getConnection();  
		   }  
  //userInput取得用户输入的信息,selectedItem取得用户选择的查询方式  
		String userInput = jtfSearch.getText().toString().trim().toLowerCase();  
		String selectedItem = jcbSearch.getSelectedItem().toString().trim();  
		System.out.println("User search:" + userInput);  
		System.out.println("selectedItem:" + selectedItem); 
		String sql = null;  
    
   //当用户点击登录时，调用以下方法去数据库做匹配  
	public void processLogin() {  
    //create connection to the database.  
		Connection con = null;   
  //Connection con = JDBCUtil.getConnection();  
  //让程序自动连接相应的数据库,以避免连接数据库时频繁改动连接程序  
		if (JDBCUtil.class == null) {  
  //连接达内Oracle数据库  
			con = JDBCUtil.getConnection();  
		} else {  
  //连接本地SQLSERVER数据库  
			con = JDBCUtil.getConnection();  
		}  
 //write sql sentence  
		String usrName = jtfUserName.getText().trim();  
		String passwrd = new String(jpfPassWord.getPassword()).trim();  
		String ident = identify.getSelectedItem().toString().trim();  
		String sql = "select * from registerXu " + "where userName=? and passWord=? and identify=?";  
		System.out.println(usrName + ":" + passwrd + ":" + ident);  
    
    
    public static Connection getConnection() {  
		      Connection connection = null; 
		      String url = "jdbc:sqlserver://localhost:1433; DatabaseName=master"; 
		      String user = "sa"; 
		      String password = "sd100301";  
    
  # 五、运行结果 
  ![](https://github.com/DING378/Homework1/blob/master/%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C/%E5%BE%AE%E4%BF%A1%E5%9B      %BE%E7%89%87_20191207163414.jpg)  
  ![](https://github.com/DING378/Homework1/blob/master/%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20191207163420.jpg) 
  ![](https://github.com/DING378/Homework1/blob/master/%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20191207163423.jpg) 
  ![](https://github.com/DING378/Homework1/blob/master/%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20191207163426.jpg) 
  ![](https://github.com/DING378/Homework1/blob/master/%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20191207163431.jpg) 
  ![](https://github.com/DING378/Homework1/blob/master/%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20191207163434.jpg) 
  
  # 六、实验心得
  本次实验开始的注册登录以及选课都是上次实验内容，但是加了写入和显示已经选好课程，自己写有点难度，最后在网上查看了下，还是选择用数据库的方式来实现，用的是SQLServer数据库。由于没有系统学习过数据库相关知识，所以写的时候参考别人写的东西，这次试验也让我学习和实践了数据库的相关知识。以后还是会多敲代码，多多练习。
  
  	  
	
	
	
	
	 
	
	
	
