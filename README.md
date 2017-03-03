php-ext-trie-filter
===================

php extension for spam word filter based on Double-Array Trie tree, it can detect if a spam word exists in a text message.

关键词过滤扩展，用于检查一段文本中是否出现敏感词，基于Double-Array Trie 树实现。

## 升级历史 
### 2017-03-03
升级为PHP7版本

### 2013-06-23
trie_filter_search_all，一次返回所有的命中词;修复内存泄露

## 依赖库

[libdatrie-0.2.4 or later](http://linux.thai.net/~thep/datrie/datrie.html)

## 安装步骤

下面的 $LIB_PATH 为依赖库安装目录，$INSTALL_PHP_PATH 为 PHP7 安装目录。   

### 安装libiconv  
（略）

### 安装libdatrie
    $ tar zxvf libdatrie-0.2.4.tar.gz
    $ cd libdatrie-0.2.4
    $ make clean
    $ ./configure --prefix=$LIB_PATH
    $ make ICONV_LIBS='/usr/local/lib/libiconv.so'
    $ make install

### 安装扩展   
    $ cd /usr/local/src/
    $ wget https://github.com/frostsky/trie_filter/archive/master.zip
    $ unzip master.zip
    $ cd trie_filter/
    $ $INSTALL_PHP_PATH/bin/phpize
    $ ./configure --with-php-config=$INSTALL_PHP_PATH/bin/php-config --with-trie_filter[=$LIB_PATH]
    $ make
    $ make install

然后修改php.ini，增加一行：extension=trie_filter.so，然后重启PHP。

## 使用示例
	<?php
	// 生成词库字典	
	$arrWord = array('word1', 'word2', 'word3');
	$resTrie = trie_filter_new(); //create an empty trie tree
	foreach ($arrWord as $k => $v) {
		trie_filter_store($resTrie, $v);
	}
	trie_filter_save($resTrie, __DIR__ . '/blackword.dic');

	// 加载字典 	
	$resTrie = trie_filter_load(__DIR__ . '/blackword.dic');

	// 要过滤的文本
	$strContent = 'hello word2 word1';
	
	// 查找一个敏感词
	$arrRet = trie_filter_search($resTrie, $strContent);
	print_r($arrRet); //Array(0 => 6, 1 => 5)
	echo substr($strContent, $arrRet[0], $arrRet[1]); //word2
	// 查找所有敏感词
	$arrRet = trie_filter_search_all($resTrie, $strContent);
	print_r($arrRet); //Array(0 => Array(0 => 6, 1 => 5), 1 => Array(0 => 12, 1 => 5))
	

	$arrRet = trie_filter_search($resTrie, 'hello word');
	print_r($arrRet); //Array()
	//在脚本结束的时候，trie tree会自动销毁，不过我们可以通过trie_filter_free在执行过程中销毁一个trie tree
	trie_filter_free($resTrie);

# PHP版本

PHP 7 or later.

Windows is not support until now.

## License

Apache License 2.0

## 致谢

本项目是在[用于检测敏感词的 PHP 扩展](http://blog.anbutu.com/php/php-ext-trie-filter)的基础上改写的。

