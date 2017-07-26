# database board

tbl_attach
```
CREATE TABLE `tbl_attach` (
  `fullName` varchar(150) NOT NULL,
  `bno` int(11) NOT NULL,
  `regdate` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`fullName`),
  KEY `fk_board_attach` (`bno`),
  CONSTRAINT `fk_board_attach` FOREIGN KEY (`bno`) REFERENCES `tbl_board` (`bno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

tbl_board
```
CREATE TABLE `tbl_board` (
  `bno` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(200) NOT NULL,
  `content` text,
  `writer` varchar(50) NOT NULL,
  `regdate` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `viewcnt` int(11) DEFAULT '0',
  `replycnt` int(11) DEFAULT '0',
  PRIMARY KEY (`bno`)
) ENGINE=InnoDB AUTO_INCREMENT=394 DEFAULT CHARSET=utf8;
```

tbl_member

```
CREATE TABLE `tbl_member` (
  `userid` varchar(50) NOT NULL,
  `userpw` varchar(50) NOT NULL,
  `username` varchar(50) NOT NULL,
  `email` varchar(100) DEFAULT NULL,
  `regdate` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updatedate` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`userid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

tbl_message
```
CREATE TABLE `tbl_message` (
  `mid` int(11) NOT NULL AUTO_INCREMENT,
  `targetid` varchar(50) NOT NULL,
  `sender` varchar(50) NOT NULL,
  `message` text NOT NULL,
  `opendate` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `senddate` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`mid`),
  KEY `fk_usersender` (`targetid`),
  CONSTRAINT `fk_usersender` FOREIGN KEY (`targetid`) REFERENCES `tbl_user` (`uid`),
  CONSTRAINT `fk_usertarget` FOREIGN KEY (`targetid`) REFERENCES `tbl_user` (`uid`)
) ENGINE=InnoDB AUTO_INCREMENT=23 DEFAULT CHARSET=utf8;
```

tbl_reply
```
CREATE TABLE `tbl_reply` (
  `rno` int(11) NOT NULL AUTO_INCREMENT,
  `bno` int(11) NOT NULL DEFAULT '0',
  `replytext` varchar(1000) NOT NULL,
  `replyer` varchar(50) NOT NULL,
  `regdate` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updatedate` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`rno`),
  KEY `fk_board` (`bno`),
  CONSTRAINT `fk_board` FOREIGN KEY (`bno`) REFERENCES `tbl_board` (`bno`)
) ENGINE=InnoDB AUTO_INCREMENT=25 DEFAULT CHARSET=utf8;
```

tbl_user
```
CREATE TABLE `tbl_user` (
  `uid` varchar(50) NOT NULL,
  `upw` varchar(50) NOT NULL,
  `uname` varchar(100) NOT NULL,
  `upoint` int(11) NOT NULL DEFAULT '0',
  `sessionkey` varchar(50) NOT NULL DEFAULT 'none',
  `sessionlimit` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`uid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

user
```
CREATE TABLE `user` (
  `pw` varchar(50) NOT NULL,
  `id` varchar(50) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
add
alter table transfer_data_dst message text;
