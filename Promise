class Promise {
    constructor(task) {
        if (typeof task !== 'function') {
            throw new Error();
        }
        this._status = 'pending';
        this._value = undefined;
        this._then = [];
        this._catch = [];
        this._invoked = false;

        let _this = this;
        function resolve(x) {
            if (_this._status === 'pending') {
                _this._status = 'resolved';
                _this._value = x;
                setTimeout(function() {
                    _this._invoked = true;
                    _this._then.forEach(function(item, index, arr){
                        item(x);
                    });
                    _this._then = [];
                    _this._catch = [];
                }, 0);
            }
        }
        function reject(x) {
            if (_this._status === 'pending') {
                _this._status = 'rejected';
                _this._value = x;
                setTimeout(function() {
                    _this._invoked = true;
                    if (_this._catch.length === 0) {
                        throw (x);
                    }
                    _this._catch.forEach(function(item, index, arr){
                        item(x);
                    });
                    _this._then = [];
                    _this._catch = [];
                }, 0);
            }
        }

        try {
            task(resolve, reject);
        } catch (e) {
            reject(e);
        }
    }

    then(fn1, fn2) {
        let _this = this,
            _fn1,
            _fn2;

        let promise = new Promise(function(resolve, reject) {
            _fn1 = function(x) {
                let result = fn1(x);
                if (result instanceof Promise) {
                    result.then(function(x) {
                        resolve(x);
                    }, function(x) {
                        reject(x);
                    });
                } else {
                    resolve(result);
                }
            };
            _fn2 = function(x) {
                let result = fn2(x);
                if (result instanceof Promise) {
                    result.then(function(x) {
                        resolve(x);
                    }, function(x) {
                        reject(x);
                    });
                } else {
                    resolve(result);
                }
            }
        });

        if (typeof fn1 === 'function') {
            if ( !this._invoked ) {
                this._then.push(_fn1);
            } else if (this._status === 'resolved') {
                setTimeout(function() {
                    _fn1(_this._value);
                }, 0);
            }
        }
        if (typeof fn2 === 'function') {
            if ( !this._invoked ) {
                this._catch.push(_fn2);
            } else if (this._status === 'rejected') {
                setTimeout(function() {
                    _fn2(_this._value);
                }, 0);
            }
        }

        return promise;
    }

    catch(fn) {
        return this.then(null, fn);
    }
}
