---
layout: post
cid: 54
title: JSP+JavaBean实现分层架构
slug: 54
date: 2018/11/21 23:59:00
updated: 2018/11/23 16:59:28
status: publish
author: admin
categories: 
  - 归档
tags: 
---


用到JavaSE部分的JDBC技术，JavaEE部分的cookie保存浏览记录，JSP+JavaBean的分层架构思想，采用Model1（JSP+JavaBean）来实现浏览商品记录的开发步骤：
用到JavaSE部分的JDBC技术，JavaEE部分的cookie保存浏览记录，JSP+JavaBean的分层架构思想

1. 实现DBHelper类，来获得数据库连接等
2. 创建实体类，对应了数据库中的表
3. 创建业务逻辑类（DAO），也就是javabean，javabean封装了针对商品的一些业务逻辑，比方说查询（全部查询，单体查询，集合查询）。
4. 创建页面层。


include指令是将源文件与include添加的文件一起编译成一个servlet文件，如果include添加的文件经常发生变化，那么每次都需要编译，同时原来页面的代码也需要一起编译，这样效率不高，如果此时使用include动作的话，只需要编译include添加进来的文件，原来页面的代码是不需要再次编译的，这样开销会小一点。

/*DBHelper类*/
```java
package util;
import java.sql.Connection;
import java.sql.DriverManager;
public class DBHelper {
	private static final String driver = "com.mysql.jdbc.Driver";
	private static final String url="jdbc:mysql://localhost:3306/shopping?useUnicode=true&characterEncoding=utf-8";
	private static final String username = "root";
	private static final String password = "";
	private static Connection conn = null;
	//静态代码块负责加载驱动
	static{
		try {
			Class.forName(driver);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	//返回数据库连接对象
	public static Connection getConnection() throws Exception{
		if(conn==null){
			conn = DriverManager.getConnection(url,username,password);
			return conn;
		}
		return conn;
	}//end getConnection method
	
	public static void main(String[] args){
		try {
			Connection conn = DBHelper.getConnection();
			if(conn!=null)
				System.out.println("连接正常");
			else
				System.out.println("连接失败");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
}
```

/*ItemsDAO类①*/
```java
package dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import util.DBHelper;
import entity.Items;

//商品业务逻辑类
public class ItemsDAO {
	public ArrayList<Items> getAllItems() throws SQLException{
			Connection conn =null;
			PreparedStatement stmt = null;
			ResultSet rs = null;
			ArrayList<Items> list = new ArrayList<Items>();
			try {
				conn = DBHelper.getConnection();
				String sql = "select * from items;";
				stmt = conn.prepareStatement(sql);
				rs = stmt.executeQuery();
				while(rs.next()){
					Items item = new Items();
					item.setId(rs.getInt("id"));
					item.setName(rs.getString("name"));
					item.setCity(rs.getString("city"));
					item.setNumber(rs.getInt("number"));
					item.setPrice(rs.getInt("price"));
					item.setPicture(rs.getString("picture"));
					list.add(item);
				}
				return list;
```

/*ItemsDAO类②*/
```java
} catch (Exception e) {
				e.printStackTrace();
				return null;
			}
			finally{
				if(rs!=null){
					rs.close();
					rs=null;
				}
				if(stmt!=null){
					stmt.close();
					stmt=null;
				}
			}
	}
	
	//根据商品编号获得商品资料
	public Items getItemsById(int id) throws SQLException{
		Connection conn =null;
		PreparedStatement stmt = null;
		ResultSet rs = null;
		try {
			conn = DBHelper.getConnection();
			String sql = "select * from items where id=?;";
			stmt = conn.prepareStatement(sql);
			stmt.setInt(1, id);
			rs = stmt.executeQuery();
			if(rs.next()){
				Items item = new Items();
				item.setId(rs.getInt("id"));
				item.setName(rs.getString("name"));
				item.setCity(rs.getString("city"));
				item.setNumber(rs.getInt("number"));
				item.setPrice(rs.getInt("price"));
				item.setPicture(rs.getString("picture"));
				return item;
			}else{
				return null;
			}
```

/*ItemsDAO类③*/

```java
} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
		finally{
			if(rs!=null){
				rs.close();
				rs=null;
			}
			if(stmt!=null){
				stmt.close();
				stmt=null;
			}
		}
	}
	
	//获取最近浏览的5条记录
	public ArrayList<Items> getViewList(String list) throws SQLException{
		ArrayList<Items> itemlist = new ArrayList<Items>();
		int iCount = 5;
		if(list!=null && list.length()>0){
			String[] arr = list.split(",");
			//如果商品记录大于5
			if(arr.length>5){
				for(int i=arr.length-1;i>=arr.length-iCount;i--){
					itemlist.add(getItemsById(Integer.parseInt(arr[i])));
				}
			}else{
				for(int i=arr.length-1;i>=0;i--){
					itemlist.add(getItemsById(Integer.parseInt(arr[i])));
				}
			}
			return itemlist;
		}else{
			return null;
		}
	}
	
}
```

