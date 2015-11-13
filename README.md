# jqfunc
最近一直在阅读jQuery源码，发现里面很多API很实用，在很多其他的地方也会用到。
所以整理出来，方便以后使用。

##type
type是个判断类型的函数，对typeof作了兼容处理。javascript共有6种类型，
分别是：number，boolean，string，undefined，function，object。

	function type ( obj ) {
		if ( obj == null ) {
			return obj + "";
		}
		// Support: Android < 4.0, iOS < 6 (functionish RegExp)
		return typeof obj === "object" || typeof obj === "function" ?
			class2type[ toString.call(obj) ] || "object" :
			typeof obj;
	}
