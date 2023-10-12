1. 一键导入功能：

   1. 数据库创建：

      根据现有配置数据源，执行建库脚本和空间属性添加脚本

   2. 数据表还原

      使用shell命令执行给定的脚本（注意：此操作需要psql文件和相应linux环境）

      ```java
          public static String exeCmd(String... commandStr) {
              String result = null;
              try {
                  //            String[] cmd = new String[] {"/bin/sh", "-c",commandStr};
                  String[] cmd = new String[commandStr.length + 2];
                  cmd[0] = "/bin/sh";
                  cmd[1] = "-c";
                  System.arraycopy(commandStr, 0, cmd, 2, commandStr.length);
                  timer.restart();
                  //开始执行
                  logger.info("开始执行命令：[{}]，开始时间为：[{}]", commandStr, DateUtil.formatDateTime(new Date()));
                  Process ps = Runtime.getRuntime().exec(cmd);
                  BufferedReader br = new BufferedReader(new InputStreamReader(ps.getInputStream()));
                  StringBuilder sb = new StringBuilder();
                  String line;
                  while ((line = br.readLine()) != null) {
                      //执行结果加上回车
                      sb.append(line).append("\n");
                  }
      
                  result = sb.toString();
                  long usedtime = timer.intervalSecond();
                  logger.info("命令：[{}]执行结束，结束时间为：[{}]，用时：[{} second]，返回结果：[{}]", commandStr,
                          DateUtil.formatDateTime(new Date()), usedtime, result);
              }
              catch (Exception e) {
                  logger.error(e.toString(), e);
              }
              return result;
          }
      ```

      

      1. 全库还原：执行整个数据库的还原脚本

      2. 单表还原：执行多个单表还原脚本

         ```java
         	/**
              * [还原数据库]
              *
              * @param hostIP
              * @param port
              * @param userName
              * @param password
              * @param databaseName
              * @param savePath
              * @param fileName
              * @param psqlPath
              * @return java.lang.String
              */
             public static String pgRestore(String hostIP, String port, String userName, String password, String databaseName,
                     String savePath, String fileName, String psqlPath) {
         
                 if (!savePath.endsWith(File.separator)) {
                     savePath = savePath + File.separator;
                 }
         
                 String cmdBuilder =
                         "PGPASSWORD=" + password + " " + psqlPath + " -h " + hostIP + " -U " + userName + " -p " + port + " -f "
                                 + savePath + fileName + " " + StringUtil.toLowerCase(databaseName);
                 return ExcuteLinux.exeCmd(cmdBuilder);
             }
         ```

         

3. 表字段添加功能

   需要添加的字段相对固定，将脚本存入 backupalter.factories 文件，由java读取文件并执行

   ```java
   /**
        * 读取 META-INF/backupalter.factories 中的内容，并执行
        */
       public void restoreFields() {
           Map<String, String> map = DBOperationsFactory.loadCreateTableFactories(this.getClass().getClassLoader(),
                   this.getClass().getSimpleName(), FACTORIES_RESOURCE_LOCATION);
           for (Map.Entry<String, String> tableName_Sqls_EntrySet : map.entrySet()) {
               String sqls = tableName_Sqls_EntrySet.getValue();
               List<String> sqlList = new ArrayList<>();
               for (String sql : sqls.split(",")) {
                   //TODO 待完善
                   sqlList.add(sql.replace("{tablename}", "tttttttt"));
               }
               //执行语句
               dbOperationsFactory.getDbThenExecFunction(dsName, reStoreDBName, db -> {
                   try {
                       return db.executeBatch(sqlList);
                   }
                   catch (SQLException e) {
                       throw new RuntimeException(e);
                   }
               });
           }
   
       }
   ```

   

4. excel数据导入功能

   读取excel中的数据，将数据存储到一键导入页面的表中

5. 表格局部刷新功能

   使用框架DataGrid中的updateRow(row,rowData)方法实现行刷新