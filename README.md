# Javascript
https://github.com/ImJane/My-javascript.git
var Base = function(){  
  
};  
  
Base.extend = function(_instance,_static){//subclass  
    var extend = Base.prototype.extend;  
  
    //build thid prototype  
    Base._prototyping = true;  
  
    var proto = new this;  
  
    extend.call(proto,_instance);  
  
    proto.base = function(){  
        //call this method from any other method to invoke that method's ancestor  
    }  
    delete Base._prototyping;  
      
    //create the wrapper for the constructor function  
    var constructor = proto.constructor;  
  
    var klass = proto.constructor = function(){  
        if(!Base._prototyping){  
            if(this._constructing || this.constructor == klass){  
                this._constructing = true;  
                constructor.apply(this,arguments);  
                delete this._constructing;  
            }else if (arguments[0] != null){  
                return (arguments[0].extend || extend).call(arguments[0],proto);  
            }  
        }  
    };  
  
    //build the clas interface   
  
    klass.ancestor = this;  
    klass.extend = this.extend;  
    klass.forEach = this.forEach;  
    klass.implement = this.implement;  
    klass.prototype = proto;  
    klass.toString = this.toString;  
    klass.valueOf = function(type){  
        //return (type == "object")?klass:constructor;  
        return (type == "object")?klass:constructor.valueOf();  
    };  
    extend.call(klass,_static);  
    //class initialisation  
    if(typeof klass.init == "function"){  
        klass.init();  
    }  
    return klass;  
};  
  
Base.prototype = {  
    extend:function(source,value){  
        if(arguments.length>1){  
            var ancestor = this[source];  
            if(ancestor && (typeof value == "function") && (!ancestor.valueOf || ancestor.valueOf() != value.valueOf())  
                && /\bbase\b/.test(value)){  
                var method = value.valueOf();  
                value = function(){  
                    var previors = this.vase || Base.prototype.base;  
                    this.base = ancestor;  
                    var returnValue = method.apply(this,arguments);  
                    this.base = previous;  
                    return returnValue;  
                };  
                value.toString = Base.toString;  
            }  
            this[source] = value;  
        }else if(source){//extending with an object literal  
            var extend = Base.prototype.extend;  
            if(!Base._prototyping && typeof this != "function"){  
                extend = this.extend || extend;  
            }  
            var proto = {toSource:null};  
            //do the "toString" and other methods manually  
            var hidden = ["constructor","toString","valueOf"];  
            // if we are prototyping then include the constructor  
            var i = Base._prototyping ?0:1;  
            while(key = hidden[i++]){  
                if(source[key] != proto[key]){  
                    extend.call(this,key,source[key]);  
                }  
            }  
  
            for(var key in source){  
                if(!proto[key]){  
                    extend.call(this,key,source[key]);  
                }  
            }  
        }  
        return this;  
    }  
};  
  
//initialise  
Base = Base.extend({  
    constructor:function(){  
        this.extend(arguments[0]);  
    }  
},{  
    ancestor:Object,  
    verson:"1.1",  
      
    forEach:function(object,block,context){  
        for(var key in object){  
            if(this.prototype[key] === undefined){  
                block.call(context,object[key],key,object);  
            }  
        }  
    },  
    implement:function(){  
        for(var i=0;i<arguments.length;i++){  
            if(typeof arguments[i] == "function"){  
                //if it's a function,call it  
                arguments[i](this.prototype);  
            }else{  
                //add the interface using the extend method  
                this.prototype.extend(arguments[i]);  
            }  
        }  
        return this;  
    },  
    toString:function(){  
        return String(this.valueOf());  
    }  
}); 
