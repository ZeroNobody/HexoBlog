---
title: TPI（tongfang profession information）个性化开发
date: 2018-12-05 09:39:17
tags: 
- TPI
- KBase
categories:
- 同方知网
---

这是一个依赖性很强（坑爹）的产品，数据库用的是知网自主研发的kbase全文检索数据库，然后衍生出一个全家桶，包括内容管理器，服务器管理，电子书加工工具，元数据编目工具，CAJ阅读器，数据转换工具，数据查询工具等。

平台框架为.NET Framework4，后台UI框架是ASP.NET MVC4，数据库KBase

## 数据库设计

内容管理器新建设计好的库信息，必须设定库代码命名规则比如这次以ZTB打头：ZTBPARTYSCHOOL、ZTBETD等。

## 业务逻辑层代码修改

登录界面后台，将刚刚新建的库发布出去。我KBase用的TPI6.5，而业务代码用的7.0，所以单库发布勾选字段点击下一步后出错
	
		2018-12-06 09:49:14,536 ERROR - KNet.Data.KBaseClient.KBaseException: KBaseException: { 服务器执行SQL语句出错, 错误码: -1109 }
	   在 KNet.Data.KBaseClient.KBaseCommand.ExecuteNonQuery()
	   在 KNet.Data.Entity.KBase.KBaseDataProvider.Insert[T](T model, String tableName)
	   在 KNet.Data.Entity.DataContext.Insert[T](T model, String tableName)
	   在 Castle.Proxies.Invocations.IDataContext_Insert.InvokeMethodOnTarget()
	   在 Castle.DynamicProxy.AbstractInvocation.Proceed()
	   在 Castle.Proxies.IDataContextProxy.Insert[T](T model, String tableName, String ctxName)
	   在 CNKI.TPI.Web.Admin.DAO.SingleDBPublishDAO.InsertPublicField(SYS_CMS_DATABASE_PUBLISH_FIELD obj) 位置 E:\CNKI\CNKI.TPI.Web_20181107\CNKI.TPI.Web.Admin.DAO\SingleDBPublishDAO.cs:行号 212
	2018-12-06 09:49:15,017 ERROR - 表SYS_CMS_DATABASE_PUBLISH_FIELD插入数据失败：FieldType:1

kbase需要升级到7.0，或者执行一下代码：

	alter table SYS_CMS_DATABASE add ISUNICODE INTEGER(10) NORMAL
	alter table SYS_CMS_DATABASE add SEARCHTYPE INTEGER(8) NORMAL
	alter table DBUSField add FieldType INTEGER(10) NORMAL
	alter table DBUSField add DataType VCHAR(32) NORMAL
	alter table DBUSField add FieldID INTEGER(10) NORMAL
	alter table DBUSField add SortNo INTEGER(10) NORMAL
	alter table DBUSField add Width INTEGER(10) NORMAL
	alter table DBUSField add SortType INTEGER(10) NORMAL
	alter table SYS_CMS_NEWS_NEWSCONTENTCONFIG add Location INTEGER(10) NORMAL
	alter table SYS_CMS_NEWS_JUMPTEMPLATE add Location INTEGER(10) NORMAL
	alter table SYS_CMS_NEWS_NEWSCONTENTCONFIG add SORTNO INTEGER(10) NORMAL
	alter table SYS_CMS_DATABASE_PUBLISH_FIELD add ISHIDDEN INTEGER(10) NORMAL
	alter table DBUSFIELD add ISHIDDEN INTEGER(10) NORMAL
	alter table SYS_CMS_SUBMIT add FieldType INTEGER(10) NORMAL
	alter table DBUSFIELD add DICFIELDNAME VCHAR(255) ASCII INDEX CHAR NORMAL DISPLAYNAME 字典名称
	alter table SYS_CMS_DATABASE_PUBLISH_FIELD add FIELDDICNAME VCHAR(255) ASCII INDEX CHAR NORMAL DISPLAYNAME 字典名称
	alter table SPORTSIAAF_METADATA add SYS_FLD_COMMENTRATE INTEGER(10) ASCII INDEX INTEGER NORMAL DISPLAYNAME 评论数量


### 首页

1.数据库列表过滤SingleDBPublishDAO.cs GetDBList方法里添加

	result = result.Where(b => b.DatabaseCode.StartsWith("ZTB")).ToList();

### 数字资源

1.SingleDBPublishDAO.cs与CategoryDAO.cs的GetPublishDBList与GetDatabaseCLSDBType方法里添加一下过滤条件

	result= result.Where(b => b.DatabaseCode.StartsWith("ZTB")).ToList();

### 首页检索条分类里的库

1.UserDBDAO.cs 的GetUnionDB方法里添加

	result = result.Where(b => b.DbCode.StartsWith("ZTB")).ToList();




