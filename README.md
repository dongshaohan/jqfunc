# jqfunc
最近一直在阅读jQuery源码，发现里面很多API很实用，在很多其他的地方也会用到。
所以整理出来，方便以后使用。

##type
type是个判断变量类型的函数。javascript中，用typeof判断变量的类型，共有6种类型，
分别是：`number`，`boolean`，`string`，`undefined`，`function`，`object`。
jQuery对它作了拓展，多了`Array`，`Date`，`RegExp`，`Error`4种，共10种。

	var class2type = {}; // 保存对象转字符串类型
	var toString = class2type.toString; // 获取对象转字符串api
	var typeArr = ["Boolean", "Number", "String", "Function", 
		"Array", "Date", "RegExp", "Object", "Error"]; // 保存对象类型

	for ( var i in typeArr ) {
		class2type[ "[object " + typeArr[i] + "]" ] = typeArr[i].toLowerCase();
	}

	function type ( obj ) {
		if ( obj == null ) {
			return obj + "";
		}
		// Support: Android < 4.0, iOS < 6 (functionish RegExp)
		return typeof obj === "object" || typeof obj === "function" ?
			class2type[ toString.call(obj) ] || "object" : 
			typeof obj;
	}

##error
	
	function error ( msg ) {
		throw new Error( msg );
	}

##isArray
	
	isArray = Array.isArray

##isFunction

	function isFunction ( obj ) {
		return type(obj) === "function";
	}

##isWindow

	function isWindow ( obj ) {
		return obj != null && obj === obj.window;
	}

##isNumeric

	function isNumeric ( obj ) {
		// parseFloat NaNs numeric-cast false positives (null|true|false|"")
		// ...but misinterprets leading-number strings, particularly hex literals ("0x...")
		// subtraction forces infinities to NaN
		return obj - parseFloat( obj ) >= 0;
	}