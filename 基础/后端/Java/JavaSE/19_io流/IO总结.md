# File

```
new File("/src/...")
```

> 这里其实就是文件里面就是一个string路径，不具备什么创建文件功能，如果想要文件操作需要进行函数的调用才行



### File函数

具体函数请查看javaAPI函数

示例如下：

```
File f1 = new File("src/static/FileInputStream");
f1.mkdir() // 创建文件夹
File f2 = new File(f1, "/create.txt");
f2.createNewFile() // 创建文件
```



### File和IO流的区别

File和IO流的区别是IO流不仅可以操作本地文件，还可以操作网络上的文件

# IO流

## 字节流

字节流有两个

- 写 ：FileOutputStream
- 读 ：FileInputStream

一个复制文件的例子

```java
// 利用字节数组赋值文件
FileInputStream fis2 = new FileInputStream(new File("src/static/FileInputStream/create.txt"));

FileOutputStream fos2 = new FileOutputStream(new File("src/static/FileInputStream/createcopy.txt"));

byte[] bytes2 = new byte[1024];
// 读写数据
int by = 0;
while((by = fis2.read(bytes2)) != -1) {
    fos2.write(bytes2, 0, by);
}
// 释放资源
fos2.close();
fis2.close();
```

> 一般来说使用字节数组进行读写，因为字节流一次只能读写一个





## 字符流

> 字节流操作的基本单元为字节；字符流操作的基本单元为Unicode码元。
>
> 当遇到中文的时候可以读多个字节
>
> 并且字符流添加了一个缓冲区，会尽可能装满数据。
>
> **字符流 = 字节流 + 字符集**



- FileReader

```
//1.创建对象
FileReader fr = new FileReader( fileName: "myiolla.txt");
//2.读取数据
char[] chars = new char[2];
int len;
//read(chars):
while((len = fr.read(chars)) != -1){
    //把数组中的数据变成字符串再进行打印	
    System.out.print(new String(chars, offset: 0,len));
}
//3.释放资源
fr.close();
```

- FileWrite

> 代码看区别

### 字符流和字节流的代码区别

- 字节流write()

  ```
  FileOutputStream fos =new FileOutputStream(name:"myio\la.txt");
  fos.write（25105）；//字节流每次只能操作一个字节fos.close();
  ```

  > 此时fos.write（b:25105）会报错，因为字节流只能读-128-127的范围，但是如果换成FileReader就正确了。

- 字符流write()

  ```
  FileWrite fos =new FileWrite(name:"myio\la.txt");
  fw.write（“你好威啊？?？"）
  fos.close();
  ```

  > 此时是正确的，因为是字符流，当然上面的25105在这里也是正确的



练习： 

1. 复制文件夹

   ```
   private static void copydir(File src, File dest)throws IOException {
   	dist.mkdir();
   	//递归
   	//1.进入数据源
   	File[] files = src.listFiles();
   	//2.遍历数组
   	for（File file :files）{
   		if(file.isfile()) {
   			//3.判断文件，拷贝
   			FileInputStream fis = new FileInputStream(file);
   			// 这里需要使用dest文件夹下的目的路径文件   new File里面是最终文件的路径
   			FileoutputStream fos = new FileoutputStream(new File(dest.file.getName()))
   			byte[] bytes = new byte[1024];
   			int len;
   			while((len = fis.read(bytes)) != -1){
   				fos.write(bytes, off: 0,len);
   		}
   		fos.close();
   		fis.close();
   		}else {
   			//4.判断文件夹，递归
   			copydir(file, new File(dest,file.getName()));
   		}
   	}
   }
   ```

   

2. 加密解密文件

   原理是异或`^` 两边相同：false，两边不同：true

   ```
   //1.创建对象关联原始文件
   FileInputStream fis =new FileInputStream(name:"myio\\girl.jpg");
   //2.创建对象关联加密文件
   FileOutputStream fos =new FileOutputStream(name:"myio\lency.jpg");
   //3.加密处理
   int b;
   while（(b=fis.read（))!=-1){
   	fos.write（b ^ 2);
   	//4.释放资源
   	fos.close();
   	fis.close();
   }
   ```

   > 这里加密解密就是`b ^ 2` 因为一个数据异或两次就会变回原来的数据

3. 修改文件中的数据

   ```
   文本文件中有以下的数据：
   2-1-9-4-7-8
   将文件中的数据进行排序，变成以下的数据：
   1-2-4-7-8-9
   //1.读取数据
   FileReader fr= new FileReader(fileName:"myiolla.txt");
   StringBuilder sb = new StringBuilder();
   int ch;
   while((ch = fr.read()) != -1){
   	sb.append((char)ch);
   }
   fr.close();
   System.out.println(sb);
   //2.排序
   Integer[] arr = Arrays.stream(sb.tostring().split( regex: "-"))
   		.map(Integer::parseInt)
   		.sorted()
   		.toArray(Integer[]: :new);
   //3.写出
   FileWriter fw = new FileWriter( fileName: "myio\la.txt");
   String s = Arrays.toString(arr).replace( ",","-");
   String result = s.substring(1, s.length() - 1);
   fw.write(result);
   fw.close();
   ```

   > 主要是排序，使用了steam流和方法引用和Lambda表达式



## 缓冲流

###  字节缓冲流 && 字符缓冲流

> 加了缓冲区提高效率，并添加了新的方法。



> 下面是一个字节缓冲流的一个复制文件代码

```
//1.创建缓冲流的对象
BufferedInputStream bis = new BufferedInputStream(new FileInputStream(name:"myio\la.txt"));
BufferedoutputStream bos = new BufferedoutputStream(new FileoutputStream( name: "myio\\copy2.txt"));
//2.拷贝（一次读写多个字节）
byte[] bytes = new byte[1024];
int len;
while((len = bis.read(bytes)) != -1){
	bos.write(bytes, off: 0,len);
}
//3.释放资源
bos.close();
bis.close()
```



字符缓冲流

```
//1.创建字符缓冲输出流的对象
BufferedWriter bw = new BufferedWriter(new FileWriter( fileName: "b.txt", append: true));
//2.写出数据
bw.write( str: "123");
bw.newLine();
bw.write( str: "456");
bw.newLine();
//3.释放资源
bw.close()
```



## 转换流

> 字符流和字节流之间的桥梁

- InputStreamReader

  > 这个是将字节流转换为字符流

- OutputStreamWriter

  > 这个是将字符流转换为字节流

> 这个了解即可，因为JDK11中FileReader添加了一个方法就可以直接转  FileWriter也加了

```
FileReader fr = new FileReader(fileName: "myio\\gbkfile.txt", Charset.forName("GBK"));
//2.读取数据
int ch;
while ((ch = fr.read()) != -1){
	System.out.print((char)ch);
}
//3.释放资源fr.close();
```

## 序列化流

> 作用：可以将Java中的对象写到本地文件中
>
> 使用提醒：
>
> 使用的时候需要将对象实现Serializable这个接口
>
> 1. 创建实现的对象，并添加**固定**版本号
> 2. 写入写出

1. 创建实现的对象，并添加**固定**版本号

   ```
   public class Student implements Serializable {
   	@Serialprivate static final 1ong serialVersionUID =-6357601841666449654L;
   	private String name;p
   	rivate int age;
   	private String address;
   }
   ```

   > 这里添加固定的版本号可以用编译器自动生成

2. 写入写出

   - 写出

   ````
   //1.创建对象
   Student stu = new Student( name: "zhangsan", age: 23);
   //2.创建序列化流的对象/对象操作输出流ObjectoutputStream oos = new ObjectoutputStream(new FileoutputStream( name: "myiolla.txt"));
   //3.写出数据
   oos.writeobject(stu);
   //4.释放资源
   oos.close();
   ````

   - 写入

   ```
   //1.创建反序列化流的对象
   ObjectInputStream ois = new ObjectInputStream(new FileInputStream( name: "myio\\a.txt"));
   //2.读取数据
   Object o[= ois.readobject();
   //3.打印对象
   System.out.println(o);
   //4.释放资源
   ois.close();
   ```

   

## 打印流

> 打印流只能写(output)，不能读

- 字节打印流
  - PrintStream 
- 字符打印流（添加了缓冲区）
  - PrintWriter 

## 解压缩流/压缩流

### 压缩流

```
//解压的本质：把压缩包里面的每一个文件或者文件夹读取出来，按照层级拷贝到目的地当中
//创建一个解压缩流用来读取压缩包中的数据
ZipInputStream zip = new ZipInputStream(new FileInputStream(src));
//要先获取到压缩包里面的每一个zipentry对象
//表示当前在压缩包中获取到的文件或者文件夹
ZipEntry entry;
while((entry = zip.getNextEntry()) != null){
	System.out.println(entry);
	if(entry.isDirectory()){
		//文件夹：需要在目的地dest处创建一个同样的文件夹
		File file = new File(dest,entry.toString());
		file.mkdirs();
	}else{
		//文件：需要读取到压缩包中的文件，并把他存放到目的地dest文件夹中（按照层级目录进行存放）
		FileoutputStream fos = new FileoutputStream(new File(dest,entry.toString()));
		int b;
		while((b = zip.read()) != -1){
			//写到目的地fos.write(b);
		}
		fos.close();
		//表示在压缩包中的一个文件处理完毕了。
		zip.closeEntry();
	}
}
zip.close();
```

### 压缩流

```
public static void toZip(File src,ZipOutputStream zos,String name) throws IOException {
	//1.进入src文件夹
	File[] files = src.listFiles();
	//2.遍历数组
	for （File file :files){
		if(file.isFile()){
		//3.判断-文件，变成ZipEntry对象，放入到压缩包当中
		ZipEntry entry = new ZipEntry( name: name +"\\" + file.getName());
		zos.putNextEntry(entry);
		//读取文件中的数据，写到压缩包
		FileInputStream fis = new FileInputStream(file);
		int b;
		while((b = fis.read()) != -1){
			zos.write(b);
	    }
	    fis.close();
		zos.closeEntry();
		}else {
			//4.判断-文件夹，递归
			toZip(file,zos, name: name + "\\I + file.getName()):
		}
	}
}
```



## Commons-io工具包

> 这里是commons的工具包，方法是静态的

![image-20231203170603705](./assets/image-20231203170603705.png)

## Hutool工具包
