Использование Getters и Setters
===============================

Приложение, которое использует библиотеку LibCanvas рассчитывает, что пользователь имеет современный браузер.
Все современные браузеры, а именно Firefox 3.5+, Opera 10+, Chrome, IE9+ имеют API для создания аксессоров.

Синтаксис очень простой:

	var object = {
		_foo: null,
		get foo () {
			return this._foo + ' | foo.getter';
		},
		set foo (value) {
			this._foo = 'foo.setter | ' + value;
		}
	};
	// Теперь к свойству foo можно обращаться как к обычному свойству:
	object.foo = 'value';
	atom.log(object.foo); // 'foo.setter | value | foo.getter'

За счёт этого можно очень прозрачно подменять получение свойства функции и инкапсулировать свойства даже после того, как API стал общедотупен и написано большое количество кода с его использованием.

Потому не стоит опасаться того, что какому-то свойству понадобится аксессор и вы не сможете поменять API - их можно добавить в любой момент не затрагивая публичный интерфейс класса, потому совершено не имеет смысла объявлять их заранее.

Допустим у нас есть класс

	var Point = atom.Class({
		initialize: function (x, y) {
			this.x = x;
			this.y = y;
		}
	});

И множество кода, где он используется:

	var p = new Point(2,3);
	this.moveTo(p.x, p.y);

Допустим, нам необходимо добавить перерисовку холста при изменении координат точек, но не изменяя API. Делаем свойство приватными (нижнее подчёркивание как префикс) и добавляем аксессоры:

	var Point = atom.Class({
		initialize: function (x, y) {
			this._x = x;
			this._y = y;
		},
		get x () { return this._x; },
		get y () { return this._y; },
		set x (newX) {
			this._x = newX;
			canvas.update();
		},
		set y (newY) {
			this._y = newY;
			canvas.update();
		},
	});

API остался прежним, а функциональность добавилась.