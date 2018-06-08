``` java
String path = "";
File file = new File(path);
if(!file.exist()) {
  file.mkdir();
  System.out.println("file create success");
}
FileWriter fw = null;
BufferedWriter bw = null;
try{
  String fileName = "";
  fw = new FileWriter(path + File.separator + fileName);
  bw = new BufferedWriter(fw);
  StringBuffer sb = new StringBuffer();
  sb.append("xxxx");
  bw.writer(sb);
  bw.flush();
}catch(Exception e) {

}finally{
  try{
    if(bw != null) {
      bw.close();
    }
  }catch(Exception e) {

  }
  try{
    if(fw != null) {
      fw.close();
    }
  }catch(Exception e) {

  }
}

java文件输出时报“拒绝访问”异常

** 这种情况是访问了一个目录而不是文件，所以会抛出这种异常 **
