# think-auth
[![GitHub issues](https://img.shields.io/github/issues/ppeerit/think-auth.svg)](https://github.com/ppeerit/think-auth/issues)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/ppeerit/think-auth/master/LICENSE)
[![Downloads](https://img.shields.io/github/downloads/ppeerit/think-auth/latest/total.svg)](https://packagist.org/packages/ppeerit/think-auth)

ppeerit\think-auth 是基于thinkphp5的简单的权限管理扩展包

##安装
```bash
composer require ppeerit\think-auth
```
##配置
扩展配置目录中新增配置文件auth.php
```php
return [
	'auth_on' => true, // 认证开关
	'auth_type' => 1, // 认证方式，1为实时认证；2为登录认证。
	'auth_group' => 'member_group', // 用户组数据表名
	'auth_group_access' => 'member_group_access', // 用户-用户组关系表
	'auth_rule' => 'member_rule', // 权限规则表
	'auth_user' => 'member', // 用户信息表
	'auth_pk' => 'uid', // 用户信息表主键
];
```
##创建数据库
权限规则表
```sql
-- ----------------------------
-- id:主键，
-- name：规则唯一标识,
-- title：规则中文名称 
-- status：状态：为1正常，为0禁用
-- condition：规则表达式，为空表示存在就验证，不为空表示按照条件验证
-- 规则附加条件,满足附加条件的规则,才认为是有效的规则
-- ----------------------------
DROP TABLE IF EXISTS `member_rule`;
CREATE TABLE `member_rule` (
`id` mediumint(8) unsigned NOT NULL AUTO_INCREMENT,
`name` char(80) NOT NULL DEFAULT '',
`title` char(20) NOT NULL DEFAULT '',
`type` tinyint(1) NOT NULL DEFAULT '1',
`status` tinyint(1) NOT NULL DEFAULT '1',
`condition` char(100) NOT NULL DEFAULT '',
`create_time` int(10) NOT NULL DEFAULT '0',
`update_time` int(10) NOT NULL DEFAULT '0',
`delete_time` int(10) DEFAULT NULL,
PRIMARY KEY (`id`),
UNIQUE KEY `name` (`name`)
) DEFAULT CHARSET=utf8;
-- ----------------------------
```
用户组表
```sql
-- ----------------------------
-- member_group 用户组表，
-- id：主键， title:用户组中文名称
-- rules：用户组拥有的规则id， 多个规则","隔开
-- status 状态：为1正常，为0禁用
-- ----------------------------
DROP TABLE IF EXISTS `member_group`;
CREATE TABLE `member_group` (
`id` mediumint(8) unsigned NOT NULL AUTO_INCREMENT,
`title` char(100) NOT NULL DEFAULT '',
`status` tinyint(1) NOT NULL DEFAULT '1',
`rules` char(80) NOT NULL DEFAULT '',
`create_time` int(10) NOT NULL DEFAULT '0',
`update_time` int(10) NOT NULL DEFAULT '0',
`delete_time` int(10) DEFAULT NULL,
PRIMARY KEY (`id`)
) DEFAULT CHARSET=utf8;
```
用户-用户组关系表
```sql
-- ----------------------------
-- member_group_access 用户组明细表
-- uid:用户id
-- group_id：用户组id
-- ----------------------------
DROP TABLE IF EXISTS `member_group_access`;
CREATE TABLE `member_group_access` (
`uid` mediumint(8) unsigned NOT NULL,
`group_id` mediumint(8) unsigned NOT NULL,
`create_time` int(10) NOT NULL DEFAULT '0',
`update_time` int(10) NOT NULL DEFAULT '0',
`delete_time` int(10) DEFAULT NULL,
UNIQUE KEY `uid_group_id` (`uid`,`group_id`),
KEY `uid` (`uid`),
KEY `group_id` (`group_id`)
) DEFAULT CHARSET=utf8;
```