# jqfunc
最近一直在阅读jQuery源码，发现里面很多API很实用，不引入jQuery的情况下，在很多其他的地方也会用到，
所以整理出来，方便以后使用。（持续更新ing）

##type
type是个判断指定参数类型的函数。javascript中，用typeof判断的类型，共有6种，
分别是：`number`，`boolean`，`string`，`undefined`，`function`，`object`。
jQuery对它作了拓展，多了`array`，`date`，`regExp`，`error`4种，共10种。

	var class2type = {}; // 保存对象转字符串类型
	var toString = class2type.toString; // 获取对象转字符串api
	var typeArr = ["Boolean", "Number", "String", "Function", 
		"Array", "Date", "RegExp", "Object", "Error"]; // 保存对象类型

	for ( var i in typeArr ) {
		class2type[ "[object " + typeArr[i] + "]" ] = typeArr[i].toLowerCase();
	}

	function isType ( obj ) {
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
		if ( isType( obj ) !== "object" || obj.nodeType || isWindow( obj ) ) {
			return false;
		}

		// Support: Firefox <20
		// The try/catch suppresses exceptions thrown when attempting to access
		// the "constructor" property of certain host objects, ie. |window.location|
		// https://bugzilla.mozilla.org/show_bug.cgi?id=814622
		var hasOwn = {}.hasOwnProperty;
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

##isArraylike
类数组判断

	function isArraylike ( obj ) {
		var length = obj.length,
			type = isType( obj );

		if ( type === "function" || isWindow( obj ) ) {
			return false;
		}

		if ( obj.nodeType === 1 && length ) {
			return true;
		}

		return type === "array" || length === 0 ||
			typeof length === "number" && length > 0 && ( length - 1 ) in obj;
	}

##extend
jQuery的extend函数非常强大且灵活

	function extend () {
		var options, name, src, copy, copyIsArray, clone,
			target = arguments[0] || {},
			i = 1,
			length = arguments.length,
			deep = false;

		// Handle a deep copy situation
		if ( typeof target === "boolean" ) {
			deep = target;

			// skip the boolean and the target
			target = arguments[ i ] || {};
			i++;
		}

		// Handle case when target is a string or something (possible in deep copy)
		if ( typeof target !== "object" && !isFunction(target) ) {
			target = {};
		}

		// extend jQuery itself if only one argument is passed
		if ( i === length ) {
			target = this;
			i--;
		}

		for ( ; i < length; i++ ) {
			// Only deal with non-null/undefined values
			if ( (options = arguments[ i ]) != null ) {
				// Extend the base object
				for ( name in options ) {
					src = target[ name ];
					copy = options[ name ];

					// Prevent never-ending loop
					if ( target === copy ) {
						continue;
					}

					// Recurse if we're merging plain objects or arrays
					if ( deep && copy && ( isPlainObject(copy) || (copyIsArray = isArray(copy)) ) ) {
						if ( copyIsArray ) {
							copyIsArray = false;
							clone = src && isArray(src) ? src : [];

						} else {
							clone = src && isPlainObject(src) ? src : {};
						}

						// Never move original objects, clone them
						target[ name ] = extend( deep, clone, copy );

					// Don't bring in undefined values
					} else if ( copy !== undefined ) {
						target[ name ] = copy;
					}
				}
			}
		}

		// Return the modified object
		return target;
	};

##each
	
	function each ( obj, callback, args ) {
		var value,
			i = 0,
			length = obj.length,
			isArray = isArraylike( obj );

		if ( args ) {
			if ( isArray ) {
				for ( ; i < length; i++ ) {
					value = callback.apply( obj[ i ], args );

					if ( value === false ) {
						break;
					}
				}
			} else {
				for ( i in obj ) {
					value = callback.apply( obj[ i ], args );

					if ( value === false ) {
						break;
					}
				}
			}

		// A special, fast, case for the most common use of each
		} else {
			if ( isArray ) {
				for ( ; i < length; i++ ) {
					value = callback.call( obj[ i ], i, obj[ i ] );

					if ( value === false ) {
						break;
					}
				}
			} else {
				for ( i in obj ) {
					value = callback.call( obj[ i ], i, obj[ i ] );

					if ( value === false ) {
						break;
					}
				}
			}
		}

		return obj;
	};