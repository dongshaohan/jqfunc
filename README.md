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
用于判断指定参数是否window对象

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

##isPlainObject
isPlainObject函数用于判断指定参数是否是一个纯粹的对象。
即该对象是否通过`{}`或`new Object`创建的。

	function isPlainObject ( obj ) {
		// Not plain objects:
		// - Any object or value whose internal [[Class]] property is not "[object Object]"
		// - DOM nodes
		// - window
		if ( jQuery.type( obj ) !== "object" || obj.nodeType || jQuery.isWindow( obj ) ) {
			return false;
		}

		// Support: Firefox <20
		// The try/catch suppresses exceptions thrown when attempting to access
		// the "constructor" property of certain host objects, ie. |window.location|
		// https://bugzilla.mozilla.org/show_bug.cgi?id=814622
		try {
			if ( obj.constructor &&
					!hasOwn.call( obj.constructor.prototype, "isPrototypeOf" ) ) {
				return false;
			}
		} catch ( e ) {
			return false;
		}

		// If the function hasn't returned already, we're confident that
		// |obj| is a plain object, created by {} or constructed with new Object
		return true;
	}

##isEmptyObject

	function isEmptyObject ( obj ) {
		var name;
		for ( name in obj ) {
			return false;
		}
		return true;
	}

##trim
去掉字符串两边空白

	function trim ( text ) {
		return text == null ? "" : "".trim.call( text );
	}