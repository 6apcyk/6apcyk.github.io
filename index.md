<html>
<head>

<script>
  //главный список апгрейдов
  //содержит все уровни по наборам параметров
  //первое значение в каждом наборе - сила (power)
  //второе - максимум энергии
  //третье - таймер, по которому накапливается энергия
  //четвертое - стоимость *текущего* уровня (не следующего!)
  var vals = [
  [10,  10,   12,    0], [11,  10,   12,    5], [12,  10,   12,   25], [12,  11,   12,   50],
  [12,  11,   11,   70], [13,  11,   11,   90], [14,  11,   11,  110], [15,  11,   11,  135],
  [15,  12,   11,  155], [15,  12,   10,  175], [16,  12,   10,  195], [16,  13,   10,  215],
  [17,  13,   10,  235], [17,  13,    9,  255], [18,  13,    9,  275], [18,  14,    9,  300],
  [19,  14,    9,  320], [19,  14,    8,  340], [20,  14,    8,  360], [20,  16,    8,  380],
  [20,  16,    7,  405], [21,  16,    7,  425], [21,  18,    7,  450], [21,  18,    6,  470], 
  [22,  18,    6,  495], [22,  20,    6,  515], [22,  20,    5,  540], [23,  20,    5,  565], 
  [23,  23,    5,  590], [23,  23,    4,  615], [24,  23,    4,  640], [25,  23,    4,  665], 
  [25,  26,    4,  690], [26,  26,    4,  720], [26,  29,    4,  745], [27,  29,    4,  775],
  [27,  32,    4,  805], [28,  32,    4,  835], [28,  35,    4,  865], [29,  35,    4,  900],
  [29,  35,    3,  930], [30,  35,    3,  965], [31,  35,    3, 1000], [32,  35,    3, 1035],
  [32,  39,    3, 1070], [33,  39,    3, 1110], [33,  43,    3, 1145], [34,  43,    3, 1185],
  [34,  43,  2.5, 1225], [35,  43,  2.5, 1270], [35,  47,  2.5, 1310], [36,  47,  2.5, 1355],
  [36,  51,  2.5, 1400], [37,  51,  2.5, 1450], [37,  55,  2.5, 1495], [38,  55,  2.5, 1545],
  [38,  55,    2, 1595], [38,  61,    2, 1650], [39,  61,    2, 1700], [39,  67,    2, 1755],
  [40,  67,    2, 1815], [40,  67,  1.5, 1870], [41,  67,  1.5, 1930], [41,  73,  1.5, 1990],
  [42,  73,  1.5, 2055], [42,  79,  1.5, 2120], [43,  79,  1.5, 2185], [43,  85,  1.5, 2250],
  [44,  85,  1.5, 2320], [44,  93,  1.5, 2390], [45,  93,  1.5, 2465], [45, 101,  1.5, 2540],
  [46, 101,  1.5, 2615], [46, 101,    1, 2695], [47, 101,    1, 2775], [47, 109,    1, 2855],
  [48, 109,    1, 2940], [48, 117,    1, 3030], [49, 117,    1, 3115], [49, 125,    1, 3205],
  [50, 125,    1, 3300], [50, 134,    1, 3395], [51, 134,    1, 3490], [51, 143,    1, 3590],
  [52, 143,    1, 3690], [52, 152,    1, 3795], [53, 152,    1, 3900], [53, 152, 0.75, 4010],
  [54, 152, 0.75, 4120], [55, 152, 0.75, 4230], [55, 161, 0.75, 4345], [56, 161, 0.75, 4465],
  [56, 170, 0.75, 4585], [57, 170, 0.75, 4710], [57, 179, 0.75, 4835], [58, 179, 0.75, 4960],
  [58, 188, 0.75, 5090], [59, 188, 0.75, 5225], [59, 197, 0.75, 5360], [60, 197, 0.75, 5500],
  [61, 197, 0.75, 5640], [62, 197, 0.75, 5785], [63, 197, 0.75, 5930], [63, 197, 0.75, 999999]
  ]

  //соответствие отображаемого времени и того, что используется для подсчётов
  var times = {
    0.75:"45s",
	1:"1m",
	1.5:"1m 30s",
	2:"2m",
	2.5:"2m 30s",
	3:"3m",
	4:"4m",
	5:"5m",
	6:"6m",
	7:"7m",
	8:"8m",
	9:"9m",
	10:"10m",
	11:"11m",
	12:"12m"
  }
  
  droprates = [0.15, 0.70, 0.85, 0.95, 1]
  dropvalues = [25, 50, 75, 100, 150]

  //перечень id картинок, закодированных в BASE64
  var images = ["powerpic","maxenergypic","cooldownpic","energypic","sapphirepic","gempic"]

  //раскодируем картинки из BASE64
  //и суём их внутрь нужных тегов (эта и следующая функции)
  function loadpage(){
    for (let i = 0; i <images.length; i++){
	  setimages(images[i]);
	}
  }
  
  function setimages(imgitem){
    let item = document.getElementById(imgitem+"data").innerText;
	imgs = document.getElementsByName(imgitem);
	for (let i = 0; i <imgs.length; i++){
	  imgs[i].src=item
    }
  }

  //проверяем правильность заполнения поля power
  function check_pow(){
	p = document.getElementById("power").value;
	if ((p % 1 === 0)&&(p >= 10)&&(p <= 63)){
	  return true;
	} else return false;
  }

  //получаем индексы значений для искомых параметров
  //в главном списке апгрейдов
  //например, ищем силу урона - power (позиция 0) со значением 23
  //и находим 23 в главном списке наборов (на их нулевой позиции)
  //на местах 27, 28, 29
  function getIndexes(value, pos) {
    let indexes = [];
    for (let i = 0; i <vals.length; i++){
		if (vals[i][pos] === value){
          indexes.push(i);
		}
      }
    return indexes;
  }

  //удаляем всё содержимое выбранного выпадающего списка
  function removeOptions(selBox) {
    while (selBox.options.length > 0) {
      selBox.remove(0);
    }
  }

  //получаем значения для энергии/таймера при изменении параметра силы (power)
  //т.е. ищем в главном списке уровней соответствия значениям тех наборов,
  //индексы которых, полученные в предыдущей функции, были переданы в эту
  //напр. были получиены индексы 2, 3, 4 - в наборах по этим индексам
  //значения макс. энергии равны 10, 11, 11
  function getETValues(items, pos) {
    let values = [], i;
    for (i = 0; i <items.length; i++){
	  val = vals[items[i]][pos];
	  //удаляем дублирующиеся значения
	  //из примера выше [10, 11, 11] останется [10, 11]
	  if (!values.includes(val)){
		values.push(val);
      }
	}
	//дополняем найденные значения их копиями
	//(нужно для соответствия отображаемого и считаемого
	//значения таймеров кулдауна
	//и абсолютно не нужно для энергии, но присутствует для
	//обеспечения совместимости
	for (i = 0; i <values.length; i++){
	  values[i] = [values[i],values[i]]
	}
    return values;
  }

  //размещаем список опций, найденный предыдущей
  //функцией в выбранном выпадающем списке
  //в примере выше мы нашли [10, 11] для индексов 2, 3, 4
  //данным индексам соответствует значение силы, равное 12
  //т.о. при выборе силы 12, в выпадающем списке для макс. энергии
  //будет предложено на выбор два значения - 10 и 11
  function addOptions(arr, selBox) {
    for (let i = 0; i <arr.length; i++){
	  let newOption = new Option(arr[i][0],arr[i][1]);
	  selBox.add(newOption, undefined);
	}
  }

  //устанавливаем соответствия кулдаунов
  //если в getETValues мы искали значения таймеров
  //и получили, соответственно, что-то вроде [[3,3],[2.5,2.5]
  //то здесь мы замещаем одно из значений на приятное глазу
  //и на выходе получаем [[3m,3],[2m 30s,2.5]]
  function setTimes(arr) {
    let values = [];
    for (let i = 0; i <arr.length; i++){
	  values.push([times[arr[i][0]],arr[i][0]]);
	}
	return values;
  }

  //отключаем выпадающий список, если в нём всего один элемент
  //т.е. банально нечего выбирать
  function switchElement(el) {
	if (el.length > 1){
	  el.disabled = false;
	} else {
	  el.disabled = true;
	}
  }
  
  //получаем пересечение двух списков
  //напр. имеем [7,8,9] и [8,9,10]
  //их пересечением будет [8,9]
  function getIntersection(arr1,arr2){
    let arr3 = []
    for (let i = 0; i <arr1.length; i++){
	  if (arr2.includes(arr1[i])){
	    arr3.push(arr1[i]);
	  }
	}
	return arr3
  }
  
  //главная функция, собирающая в себе кучу всего, что было выше
  //и формирующая в итоге селектора трёх значений: силы, энергии, времени,
  //зависящие друг от друга
  function processETOptions(arg=0) {
    let p = Number(document.getElementById("power").value);
	let e = document.getElementById("maxenergy");
	let ev = Number(document.getElementById("maxenergy").value);
	let t = document.getElementById("time");
	let tv = Number(document.getElementById("time").value);
	if (!check_pow()){
	  e.value = '';
	  t.value = '';
	  return;
	}
	let pows = getIndexes(p, 0);
	let enrgs = getIndexes(ev, 1);
	let cooldowns = getIndexes(tv, 2);
	let n = []
	if (arg==0){
	  removeOptions(e);
	  let ETVals = getETValues(pows, 1);
	  addOptions(ETVals, e);
	  removeOptions(t);
	  ETVals = getETValues(pows, 2);
	  let converted = setTimes(ETVals);
	  addOptions(converted, t);
	}
	else if (arg==1){
	  n = getIntersection(pows,enrgs);
	  removeOptions(t);
	  let ETVals = getETValues(n, 2);
	  let converted = setTimes(ETVals);
	  addOptions(converted, t);
	}
	else{
	  n = getIntersection(pows,cooldowns);
	  removeOptions(e);
	  let ETVals = getETValues(n, 1);
	  addOptions(ETVals, e);
	}
	switchElement(e);
	switchElement(t);
  }

  //отключение более низких по приоритету опций,
  //если текущая опция изменила своё состояние от дефолтного
  function disableoption(arg){
    if (arg>0){
	  let s = document.getElementById("s"+(arg-1));
	  if (s.type == "number"){
	    if (!s.disabled){
		  dmg[arg-1] = s.value
		}
		s.value = Number(s.max) + 1
	  } else {
	    if (!s.disabled){
		  dmg[arg-1] = s.checked
		}
		s.checked = true
	  }
	  s.disabled = true;
	  checkoption(arg-1);
	}
  }
  //включение более низких по приоритету опций,
  //если текущая опция вернулась в дефолтное состояние
  function enableoption(arg){
    if (arg>0){
	  let s = document.getElementById("s"+(arg-1));
	  if (s.type == "number"){
	    s.value = dmg[arg-1]
	  } else {
		s.checked = dmg[arg-1]
	  }
	  s.disabled = false;
	  checkoption(arg-1);
	}
  }
  //функция, вкупе с предыдущими двумя рекурсивно используемая для 
  //взаимосвязи значений нанесенного урона на всех трёх этапах 
  //блиц-события с учётом приоритетности
  //если пользователь выставляет что-то на более высоком уровне, то
  //предыдущие уровни автоматически отключаются 
  //если поставлена галка "этап 1 завершён", то значение урона, нанесённого в этом этапе
  //автоматически выставляется в 1000
  //если введено какое-то значение в этап 3, то этапы 1 и 2 автоматически
  //считаются завершенными и т.д. и т.п.
  var dmg=[0,0,0,0,0];
  var prevstate=[0,0,0,0,0];
  function checkoption(arg){
    let s = document.getElementById("s"+arg);
	if (s.type == "number"){
	  if (s.value >0){
	    if (prevstate[arg]==0){
		  disableoption(arg,1);
		  prevstate[arg]=1;  
		}
	  } else {
	    enableoption(arg,0);
		prevstate[arg]=0;
	  }
	} else {
	  if (s.checked){
  	    disableoption(arg,1);
  	  } else {
	    enableoption(arg,0);
	  }
	}
  }
  
  function randomdrop(num=1){
    let sum = 0, tmp1, tmp2;
	for (let i = 0; i < num; i++){
	  tmp1 = Math.random();
	  for (let j = droprates.length - 1; j >= 0; j--){
	    if (tmp1 <= droprates[j]){
		  tmp2 = dropvalues[j];
		} else {
		  break;
		}
	  }
	  sum += tmp2
	}
	return (sum)
  }

  //считалка того, сколько времени понадобится на завершение события
  var sapphiresavg = 60;
  const bosshealth = 61000;
  var drop;
  function calctime(){
    var totaldmg = 0;
    var timetaken = 0;
    var sapphiresleft = 0;
    var storedenergy = 0;
    var helpers = 0;
  	let p = Number(document.getElementById("power").value);
	let e = Number(document.getElementById("maxenergy").value);
	let t = Number(document.getElementById("time").value);
	drop = document.getElementById("drop1").checked;
	let pows = getIndexes(p, 0);
	let nrgs = getIndexes(e, 1);
	let cooldowns = getIndexes(t, 2);
	let n, i;
	for (i = 0; i <pows.length; i++){
	  if ((nrgs.includes(pows[i]))&&(cooldowns.includes(pows[i]))){
	    n = pows[i];
		break;
	  }
	}
	if (Number(document.getElementById("help50").checked)){
	  helpers += 50
	}
	if (Number(document.getElementById("help75").checked)){
	  helpers += 75
	}

	sapphiresleft = Number(document.getElementById("cursapphires").value);
	storedenergy = Number(document.getElementById("curenergy").value);
	totaldmg += Number(document.getElementById("s0").value);
	totaldmg += Number(document.getElementById("s2").value);
	totaldmg += Number(document.getElementById("s4").value);
	for (i = n; i <vals.length; i++){
	  while ((sapphiresleft < vals[i+1][3])&&(totaldmg < bosshealth)) {
        totaldmg += vals[i][0] + helpers;
		if (drop){
		  sapphiresleft += sapphiresavg;
		} else{
		  sapphiresleft += randomdrop();
		}
		if (storedenergy >0){
		  storedenergy -= 1;
		} else {
		  timetaken += vals[i][2];
		}
	    if (totaldmg >= bosshealth){
		  checkforgems(i,totaldmg,helpers,storedenergy,timetaken,sapphiresleft);
	      break;
	    }
      }
	  if (totaldmg >= bosshealth){
	    break;
	  }
	  checkforgems(i,totaldmg,helpers,storedenergy,timetaken,sapphiresleft);
	  sapphiresleft -= vals[i+1][3];
	  storedenergy += vals[i+1][1] - vals[i][1]
	}
	result = document.getElementById("result");
	marginoferror = timetaken*0.025;
	print1 = Math.trunc(timetaken / 60)+'h '+ Math.trunc(timetaken % 60) +'m';
	let tmp = Math.trunc(marginoferror / 60);
	if (tmp>0){
	  print2 = Math.trunc(marginoferror / 60)+'h '+ Math.trunc(marginoferror % 60) +'m';
	} else {
	  print2 = Math.trunc(marginoferror % 60) +'m';
	}
	if (drop){
	  result.innerText = print1 + '    (±'+print2+')';
	} else {
	  result.innerText = print1;
	}
	
	setgems();
  }
  
  fixedv=[5000,10000,25000,50000]
  gemtimes = [9999,9999,9999,9999]
  gemindexes = [-1,-1,-1,-1]
  function checkforgems(index,totaldmg,helpers,storedenergy,timetaken,sapphiresleft){
    while ((sapphiresleft<50000)&&(totaldmg<bosshealth)){
      if (storedenergy>0){
	    storedenergy -= 1;
		totaldmg += vals[index][0]+helpers;
		if (drop){
		  sapphiresleft += sapphiresavg;
		} else{
		  sapphiresleft += randomdrop();
		}
	  } else {
		let mult = Math.ceil((bosshealth - totaldmg)/(vals[index][0]+helpers));
	    totaldmg += mult*(vals[index][0]+helpers)
		timetaken += mult*vals[index][2];
		if (drop){
		  sapphiresleft += mult*sapphiresavg;
		} else{
		  sapphiresleft += randomdrop(mult);
		}
	  }
	}
	for (i = 0; i <fixedv.length; i++){
	  if (sapphiresleft >= fixedv[i]){
	    if (timetaken <= gemtimes[i]){
		  gemtimes[i] = timetaken;
		  gemindexes[i] = index;
		}
	  }
	}
  }
  
  function setgems(){
    for (i = 0; i <gemindexes.length; i++){
	  if (gemindexes[i] > -1){
	    fillgemtarget(gemindexes[i], gemtimes[i], "gem"+fixedv[i])
		gemindexes[i] = -1
		gemtimes[i] = 9999
	  } else {
	    fillgemnull("gem"+fixedv[i])
	  }
	}
  }
  
  //заполняет таблицу камней значениями...
  function fillgemtarget(index, time, gemid){
    row = document.getElementById(gemid);
	row.children[1].innerText = Math.trunc(time / 60)+'h '+ Math.trunc(time % 60) +'m';
	let tmp1 = time*0.025 //90% значений обретаются не далее, чем в 2,5% от среднего
	let tmp2 = Math.trunc(tmp1 / 60)
	if (tmp2 > 0){
	  tmp2 = tmp2+'h '+ Math.trunc(tmp1 % 60) +'m';
	} else {
	  tmp2 = Math.trunc(tmp1 % 60) +'m';
	}
	if (drop){
	  row.children[2].innerText = '±'+tmp2;  
	} else {
	  row.children[2].innerText = '';
	}
	row.children[3].innerText = vals[index][0];
	row.children[4].innerText = vals[index][1];
	row.children[5].innerText = times[vals[index][2]];
  }
  //...или ставит там "низзя", если такое число камней невозможно 
  //получить при выбранных условиях
  function fillgemnull(gemid){
    row = document.getElementById(gemid);
	row.children[1].innerText = "Impossible";
  }
  
</script>
</head>
<!--<body onLoad="setInterval(function(){ return checkETOptions();}, 500);">-->
<body onload="loadpage()">
<span id="powerpicdata" style="display:none"> data:image/png;base64,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</span>

<span id="maxenergypicdata" style="display:none"> data:image/png;base64,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</span>

<span id="cooldownpicdata" style="display:none"> data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACgAAAAqCAYAAADBNhlmAAAACXBIWXMAAAsTAAALEwEAmpwYAAAKT2lDQ1BQaG90b3Nob3AgSUNDIHByb2ZpbGUAAHjanVNnVFPpFj333vRCS4iAlEtvUhUIIFJCi4AUkSYqIQkQSoghodkVUcERRUUEG8igiAOOjoCMFVEsDIoK2AfkIaKOg6OIisr74Xuja9a89+bN/rXXPues852zzwfACAyWSDNRNYAMqUIeEeCDx8TG4eQuQIEKJHAAEAizZCFz/SMBAPh+PDwrIsAHvgABeNMLCADATZvAMByH/w/qQplcAYCEAcB0kThLCIAUAEB6jkKmAEBGAYCdmCZTAKAEAGDLY2LjAFAtAGAnf+bTAICd+Jl7AQBblCEVAaCRACATZYhEAGg7AKzPVopFAFgwABRmS8Q5ANgtADBJV2ZIALC3AMDOEAuyAAgMADBRiIUpAAR7AGDIIyN4AISZABRG8lc88SuuEOcqAAB4mbI8uSQ5RYFbCC1xB1dXLh4ozkkXKxQ2YQJhmkAuwnmZGTKBNA/g88wAAKCRFRHgg/P9eM4Ors7ONo62Dl8t6r8G/yJiYuP+5c+rcEAAAOF0ftH+LC+zGoA7BoBt/qIl7gRoXgugdfeLZrIPQLUAoOnaV/Nw+H48PEWhkLnZ2eXk5NhKxEJbYcpXff5nwl/AV/1s+X48/Pf14L7iJIEyXYFHBPjgwsz0TKUcz5IJhGLc5o9H/LcL//wd0yLESWK5WCoU41EScY5EmozzMqUiiUKSKcUl0v9k4t8s+wM+3zUAsGo+AXuRLahdYwP2SycQWHTA4vcAAPK7b8HUKAgDgGiD4c93/+8//UegJQCAZkmScQAAXkQkLlTKsz/HCAAARKCBKrBBG/TBGCzABhzBBdzBC/xgNoRCJMTCQhBCCmSAHHJgKayCQiiGzbAdKmAv1EAdNMBRaIaTcA4uwlW4Dj1wD/phCJ7BKLyBCQRByAgTYSHaiAFiilgjjggXmYX4IcFIBBKLJCDJiBRRIkuRNUgxUopUIFVIHfI9cgI5h1xGupE7yAAygvyGvEcxlIGyUT3UDLVDuag3GoRGogvQZHQxmo8WoJvQcrQaPYw2oefQq2gP2o8+Q8cwwOgYBzPEbDAuxsNCsTgsCZNjy7EirAyrxhqwVqwDu4n1Y8+xdwQSgUXACTYEd0IgYR5BSFhMWE7YSKggHCQ0EdoJNwkDhFHCJyKTqEu0JroR+cQYYjIxh1hILCPWEo8TLxB7iEPENyQSiUMyJ7mQAkmxpFTSEtJG0m5SI+ksqZs0SBojk8naZGuyBzmULCAryIXkneTD5DPkG+Qh8lsKnWJAcaT4U+IoUspqShnlEOU05QZlmDJBVaOaUt2ooVQRNY9aQq2htlKvUYeoEzR1mjnNgxZJS6WtopXTGmgXaPdpr+h0uhHdlR5Ol9BX0svpR+iX6AP0dwwNhhWDx4hnKBmbGAcYZxl3GK+YTKYZ04sZx1QwNzHrmOeZD5lvVVgqtip8FZHKCpVKlSaVGyovVKmqpqreqgtV81XLVI+pXlN9rkZVM1PjqQnUlqtVqp1Q61MbU2epO6iHqmeob1Q/pH5Z/YkGWcNMw09DpFGgsV/jvMYgC2MZs3gsIWsNq4Z1gTXEJrHN2Xx2KruY/R27iz2qqaE5QzNKM1ezUvOUZj8H45hx+Jx0TgnnKKeX836K3hTvKeIpG6Y0TLkxZVxrqpaXllirSKtRq0frvTau7aedpr1Fu1n7gQ5Bx0onXCdHZ4/OBZ3nU9lT3acKpxZNPTr1ri6qa6UbobtEd79up+6Ynr5egJ5Mb6feeb3n+hx9L/1U/W36p/VHDFgGswwkBtsMzhg8xTVxbzwdL8fb8VFDXcNAQ6VhlWGX4YSRudE8o9VGjUYPjGnGXOMk423GbcajJgYmISZLTepN7ppSTbmmKaY7TDtMx83MzaLN1pk1mz0x1zLnm+eb15vft2BaeFostqi2uGVJsuRaplnutrxuhVo5WaVYVVpds0atna0l1rutu6cRp7lOk06rntZnw7Dxtsm2qbcZsOXYBtuutm22fWFnYhdnt8Wuw+6TvZN9un2N/T0HDYfZDqsdWh1+c7RyFDpWOt6azpzuP33F9JbpL2dYzxDP2DPjthPLKcRpnVOb00dnF2e5c4PziIuJS4LLLpc+Lpsbxt3IveRKdPVxXeF60vWdm7Obwu2o26/uNu5p7ofcn8w0nymeWTNz0MPIQ+BR5dE/C5+VMGvfrH5PQ0+BZ7XnIy9jL5FXrdewt6V3qvdh7xc+9j5yn+M+4zw33jLeWV/MN8C3yLfLT8Nvnl+F30N/I/9k/3r/0QCngCUBZwOJgUGBWwL7+Hp8Ib+OPzrbZfay2e1BjKC5QRVBj4KtguXBrSFoyOyQrSH355jOkc5pDoVQfujW0Adh5mGLw34MJ4WHhVeGP45wiFga0TGXNXfR3ENz30T6RJZE3ptnMU85ry1KNSo+qi5qPNo3ujS6P8YuZlnM1VidWElsSxw5LiquNm5svt/87fOH4p3iC+N7F5gvyF1weaHOwvSFpxapLhIsOpZATIhOOJTwQRAqqBaMJfITdyWOCnnCHcJnIi/RNtGI2ENcKh5O8kgqTXqS7JG8NXkkxTOlLOW5hCepkLxMDUzdmzqeFpp2IG0yPTq9MYOSkZBxQqohTZO2Z+pn5mZ2y6xlhbL+xW6Lty8elQfJa7OQrAVZLQq2QqboVFoo1yoHsmdlV2a/zYnKOZarnivN7cyzytuQN5zvn//tEsIS4ZK2pYZLVy0dWOa9rGo5sjxxedsK4xUFK4ZWBqw8uIq2Km3VT6vtV5eufr0mek1rgV7ByoLBtQFr6wtVCuWFfevc1+1dT1gvWd+1YfqGnRs+FYmKrhTbF5cVf9go3HjlG4dvyr+Z3JS0qavEuWTPZtJm6ebeLZ5bDpaql+aXDm4N2dq0Dd9WtO319kXbL5fNKNu7g7ZDuaO/PLi8ZafJzs07P1SkVPRU+lQ27tLdtWHX+G7R7ht7vPY07NXbW7z3/T7JvttVAVVN1WbVZftJ+7P3P66Jqun4lvttXa1ObXHtxwPSA/0HIw6217nU1R3SPVRSj9Yr60cOxx++/p3vdy0NNg1VjZzG4iNwRHnk6fcJ3/ceDTradox7rOEH0x92HWcdL2pCmvKaRptTmvtbYlu6T8w+0dbq3nr8R9sfD5w0PFl5SvNUyWna6YLTk2fyz4ydlZ19fi753GDborZ752PO32oPb++6EHTh0kX/i+c7vDvOXPK4dPKy2+UTV7hXmq86X23qdOo8/pPTT8e7nLuarrlca7nuer21e2b36RueN87d9L158Rb/1tWeOT3dvfN6b/fF9/XfFt1+cif9zsu72Xcn7q28T7xf9EDtQdlD3YfVP1v+3Njv3H9qwHeg89HcR/cGhYPP/pH1jw9DBY+Zj8uGDYbrnjg+OTniP3L96fynQ89kzyaeF/6i/suuFxYvfvjV69fO0ZjRoZfyl5O/bXyl/erA6xmv28bCxh6+yXgzMV70VvvtwXfcdx3vo98PT+R8IH8o/2j5sfVT0Kf7kxmTk/8EA5jz/GMzLdsAAAAgY0hSTQAAeiUAAICDAAD5/wAAgOkAAHUwAADqYAAAOpgAABdvkl/FRgAAEMpJREFUeNqkmXlwXld5xn/nnLt9i3bJsmXHSyw7jh1vdUyWAccxmaQJZEoIJWUty0BLW2ACdGiB0ikzXegfZYa20GlLOzBhSEjIAo1Tkjh2YgfH8b7FtixZtiRLnyzpk/RJ33bvPeftH9I4hGYKlDNzZu5f5z73fd/nvM/7XDVRjXnjUqRJFUFoDBu4FByjWjc0pB3kpInekZimXEDOt5wb7GdJS57mjlZcvopH0NpgG5+dLc2s7x8YqFnrCIIAEUE5MHiNKdHDU/XwfcXiOEopftny+E2X0sT1GerZy2Sl8eM53bol29KK9sNgYHAElyq0eDin8ELNQF/P5p/tf2V1NhP0/CrH/78BCoJ1gojTuEB1ylo7XCiOfv/Aj8avWbK0vbW9E5TCOks+n0OLJvQ9jKeve/yHj37yrdtv/0KtXv2lUfR+TVQ4wXci60PPW9nZ1LgjG0bL8tlcEBpf+b5M5pvyMl4s0j9YoDBapHB5jDRxtLd2smzpQuq1KYrT5ej5519cvOSaBZetdb8pQMGJALIMpT6wamHu7ijyN2d8cgtbVlBNIE2hVo7pbF7GyrevwAeqQNUJgwOjHDhwjBPHT/DKj16SuFpWYnTgB7o119B0OU1TAIw2WGcRkV8DoAJEdS/wmx9sCBoeCHXUVotTCoVxzp0dYKQwQaXsKJcrpEmdMPLJNURk8j5dS9q5tnspS5d30thyC5u3rmSsMK1eeflw7eSZ80F39yr7th3bmJ2ZQRRU62Uaso0EfvAGkOrNWVzBIV5k/E94Pl9Tvt9emBjj6JFTHDp0ktErJRQhYZCnsbGNhnyewHMkts5UaZqZ8gxxGuPEcu2KpVx//Rqam5toaWpDo+g7f9ae7zn3zVTrP+vv74vTUPPYC9/lLz/2eTavv5lavfp/RVDQSi3QIv+Yifz3lqo+z//0MC/t3cNseYbu1ddy12/fxKrVK1mwoBltBKM9PO0hAvU4pR5bRq9M0dfXz6lT/Tz5+F6iMMstN21g46bVrFrVbZYvWfjgq0cObxxQfKyWpJecc/z8/t8Ar7LJLQv88NF8GGy9cGmEh77/PK/1XubGrRu4+863sGR5G75RpEBCDMQoEgIJSAQyuYBsDlpaMqy/bhE7br+J/r4Rdj6zm//a+QT9F69jx/bbaW7Mc/OWt+xobWre+cTe3e9xzp15U5IobeeI4AUESi2IHT/0jbf10MkhvvNvDxOqKp/77LvYtPF6AmJmZIaKhIh4KOWTKgOkGIkRpRAUSjQiCqcU2Yzm5huWsHL5u3nkMcWLu19lYnSW++5/Dy2tzazbcOPaGZLHn9r90L3ipPcXAZov/sVXUCisMtoT+V7WeHecOH2Rf/rn/2TBgnY+8Xv30amEpsYI8VNmVcIk4KsA5UBrjRFLoh3JfBIMoJXCigMRYusIA58FHZ20ty3k9OnznD13npXdK9CeZsU1i9oDF29qzDc/tqhzcZzYZI6gCvTcNeJQtvpZpb37Lg1P89BDj7NwUTuf+pP3s2bTCs49tYddf/y3zJ4eJEuOnAqIlcUYIYprKBHKQBXBInNni2C0wuLQCkKlkTRlZfcK7r//d0iSKv/9zNMkSUylUuP9931o29h46ev7Xj2ItpBUY5JqjBZxOGqrfZ1+UQk89eNdlKspH/r999DelmfKWbZ/5iPYasxP3v4pil/5d7p6R2lBU6eGMxZsTIQlxBGIw7cO7YQEIdGaWW2ZVgleLqRWq7J8xVJuu/1WLg708eqrB1H4aB2y8frr/6A2U35nrVLDJhabWDTK4fvJH4ZevvPE6X6OnzjHO++9kxXLOqlInVjqTDVH7PibP2VV53J6//pfOPqOzxF/60csnE0ITUDe92gSiHBocWglaC2AJSbFztemKLAupVyZYuPGdWzevJGjR44xNTlDmsLG9ZvMzFTpq9/+1ncbKrOzjF8Zw3z+yw92GfQ30JnGhx/ZSRDleeCBuxBPgYpBK6wIQWsTbauWMfjsfsKBSxR37uPSviNkGrK0dl2DFwZYHHU9X3tKUErjAZE4sghxpc70THmuMymhva2VEyfOojCsXLkcrYRM4C8+d+L08KKuRa+Kc+jAy9we+k1Lhi5Pc3FglK1bN5CNFAkxAtTn66qKI7hjC6v//jMUGkI8HPm9hzn3ga+y/8NfovLsEZpriggBDUprkBQlydwJCqwSjFH4gUIkpqW1iVWruum/MEBlNiaup6xduw5fq48eOXCopTw9g7aamz18+i4MUqtZutdcSw0QYhIcGo1TmjKKMeos+vDdXPfJ+5mlSqA07QlUfvIyL3zgC+z90j+Qn6wRKEMiCU7NMXtWwTjCNHWcTUEcqBSUsGzZcsbHi8yUymjtE0VZNm5ad/3eF/dunyxOoo3T6y0wWpgin2+krSVCiHEkKDwCp4nEYMThY6iQ0P3nHyS650aKUsFH0awj3EyNMJNBsj6zJKRK8B00iaYFRZ4UV66htEIkxdoEpaC9vQPfCxkfnwQUcRyzfPnyTDYbbC9cmTDapeUVdREmpsp0dLQRhAZNgofBJ0Arg5MYUY46jppAta2T9X/3VcrrbqAkmlEPmj//PrZ+9TPEYRaDwcMHFeKcD0mAjn1sFZQOQIeAh00dnucRZSImSyVSLE5Da+cyMNm1Rw4fXay1JhJlSWxM6mKsS9AoNB4CWOVwGlIcFsGqgGk03vp1vOXrX+ZMc0DLu7Zz45f/iMnIUBOHJwaNwQKJ9hHPpxZDnAqCxjmNUj5K6auC1YpgAkPsEkyUx4+yXWPjk12es2Aw5HJZisWJuTYFc4Utbu5Gn2/a3nyXqCAUqNN5xybe9r2/omNNNyofIJKilQKZYz5otBI8FKVSCWtTtA6uil9QWGtJbUoYGKxzaOWhUIShn5m4MtimK+WEDIrW1jyTU0VqtTqacB7K68sHIgeRhVA0CTAdQNe927GrOklF0KKuRl3mO4pzQqWeMD09PR8tmd8KpQzlcplKZYaW1oa5j8MwNTU9/0rbqIeHxqwGll6ziHq9wuBQYV7iGMDNRQXwHIQWAguRWPIiKCxlWyWWlFRSnDjEOZQoFAqHoLVmanKSer2O4nWASim08hgeHiHKBLR3tBDHMVp5TEwUERELEunz5wemLbBy1TU0Nzdy+tRZNKBE4yTFOIfvQAvz0QGlFL5AIApPQSigUSg9Vw9ziljwlKZaqTA+PoExBtTcsKWUAjHYVHGh7yJdXQtoas5hbYrRAcMjIzgnVdCePnyoZ6CSOJqyHps2r+OV/ccZGJ7GVxpPewRojANRkBqoeFBVQqqhroSahlSp+etDcE7QWqGVwVrH4NAQSZrMB05QCqxNCYKI3t6LjBQKXLemG1SCH3gUiyUGLg1irUwCie7pK7/Wf3EAA2zfdjNBmOPxJ14ksSCSImqO+qmGREOswM0nKlZQARLF1Tki8Py59Ipj4PIg1WoVPc/WuY+wBEFAuVJmz+4XWbRwEdddt4o0jTHG0NPTS7FYpDRbKYCu6cWLw8NPP70HByxoa+Tuu+7i6KFT7HphL6HySbSirCHhda0XCWRT8OdTKzIn2Yw2CEItrjE0PMTMTAljDMaYq+A832CMZs/uPZRKs7z1rdvwPI/E1onThIMHD5Mkzk5OlQugZvW0NL304kuHRs72XsQHtt+2jrdtu4knHt/JnpePYcih8OeUspt7CpzgW0ckECF4osB5CD7VumVkZIKp4ixaeYBCEJyz+F4ebI5dz+3n9Kkett12K0uXd1CPSzTlmzh99ByX+wvYpD5UmZ0YDXMNk6bQcf90WB7aWLkyuvHmW28hEyq6Vy+mUKjy06eP4pyie8Uicl4AonBWcFpIPcEph0Iw2se5gMmpKiPDY5RnqoC5Kv21MfhhyMy0x7PPHOXosTPcfOtN3HTrOhI7RhQqpscqPPrQT5G6ZmTo2P7C5dOnPM8cNd3XvpfANE9ODB39SD1WbLlxDX7gcf26VVSqJZ7ZuYuBixN0dS2ivTWHpw2CAWXQyiAOZsszDBeGGL1SIEkqeL5BKY3RAVHUgGcy9J4f4sdPPM/g4Ag73n4L227bgnU1fBNCGvHwQ08xfHkMrWT6+PF9P4nj2UIYZvebTNvnmKksuZgLa+v7e/at1X4LG29YRuBr1m5YTlt7J0cP9/DSvgMUi7MEYZZsFIHyqFSFsfESY2NTpGlKJsoQRQ34XgajQ2o1x8X+IV7YtY/9Lx+iuamJd75jBxs2rqCWlDB4hLqZJx/bxbHDp2hqiOi/0LN7cPD0a77vnQr8XK/ads83EDFE3vgN2hV21xPX/sEP3899v3sXeDUgYHh4huee28XxYyeI45Tm5lYWdy0ln28hl2sgn2/AeBabpsRxwvT0FOPj4wwPDzM5NUlDQwMb1m9g86Z1NDeHVKozaOORxoYnH32WwwdO0NKcozQ9NrDvpT3/Wo+HprKZph8oHRTVux/49Hyz9iiWSp9uzOhv1urC1pu2cOc9t9HckcFJAqK5cmWS3vMXGb48zmihRLmU4HtZUAqlHc6lWJeiFeTzEQu7FrCyezlLlnTR2NiAjadxUiPwGxm+PMnTT+2i99wF8vkcSuq1/T879J0rhb6LxsQHvSD/IiKoO+/9xNV+m6SWmdnCfyzqWPbRclnT0bmIO+65kTXrlxJFEc6BtUISO2rVlNmZGjMzNer1KtZV8AOfMAhobGwkl8uSyUQY4+Gw4CAKUqqz0xzYf56X956iNFkmlzfArJw8eeqRC+cuHIRsUZv6I0pJFcD7efvLM4YLFwYfrNfVwpVL191dKtb54Q+e55oDndy4ZROr11xLU3Me37OEYZ3GJh8hPydApTzX3kw0x1ylMVrhnMNXAeOTRQ6dOcHRg0coDFfAZcnnIqybca+dO/7UhfNHDmqlUqTzOUirIvGcoLj9ro+/0TpSisL4+GIS++3Vq2+4N7EB5XqMqIQFC5pY2b2E1atX0NHZTGNTDj8whKGH5ynS1OEcpImjWk0olSoMDxXo6eml/8IlZouzeM4QRBHaQJLW4pOvnXl8sO/kAVTR83Sw09rsa/NabA7PLwIE0MpjcOByV61e/drqtVven8k0ZeK0TmprxHEZbYRsLqK9rY3mlhZ8LyDw8yhlqNUqVCoVpqenmJoqUqvXsTYmCAIyqhljDX4UMzVTuHzk2ImniqNjvUrVtVDeBd4xxRsd1zcFKE6hdMiF3t7WkeHhj67dsPpDS5cuXut7gW9TwQrYRIjjFOsERCPCXGo1KA2+7+F5GutSPM/g+T6kZSSeLQ8MXnzltTOnd8fxbB1UVSR4ThnVAxZEv9GbWdH9W2/iwClENDZNqyNDg0erlemzQwPnSyKxn438xtD3A88YfN8n9EPCwCMIFJmMJooMga/xjOB7EAQacQmV2fLElULfK6dPvfTkpUvnjidxySnsAGKfRgeDr1vV6ld3WK1zaO3Xlq5Y+Xzv2eN9J46d3N+bu7ixq7NrZUNL66q21o523/eatDZZz4gBOy+5XOKcVKamK5PFianhsYlSz/jo1KBLCiWlJpWInjQmewylTzgbp7+RRy0IiCPf0NRvtRoMo8zBC0MDa5LzZ5cEXtARRlGT8UNfiTUiosQlVlCJiKtXqvWyTZJYaxEdBHUvcOPiojM2TXq01lUnV4eT/z/AqyLdOdAmzTe39OWy+b7CUG+jFbrK8USHK7tWSHNYGyGxhzYOrWJcUqExmtY2P9a+eNFQceDyhLM1eX02+eU/cv5nAJjVo+rv3l8uAAAAAElFTkSuQmCC</span>

<span id="energypicdata" style="display:none"> data:image/png;base64,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</span>

<span id="sapphirepicdata" style="display:none"> data:image/png;base64,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</span>

<span id="gempicdata" style="display:none"> data:image/png;base64,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</span>

<h1>Blitz calculator</h1>
  <p><table style="border-collapse: collapse;"><thead>
  <tr>
  <th align="center"><img name="powerpic" src="" alt="Power" /></th>
  <th align="center"><img name="maxenergypic" src="" alt="Max energy" /></th>
  <th align="center" style="width:85px"><img name="cooldownpic" src="" alt="Cooldown time" /></th>
  <th style="border-left: 3px double #000;" align="right" ><img name="energypic" src="" alt="Current energy" /></th>
  <th align="center"><img name="sapphirepic" src="" alt="Current sapphires" /></th>
  </tr></thead>
  <tbody><tr>
  <td align="center"><input id="power" type="number" value="10" min="10" max="63" style="width:50px" onchange="processETOptions()"/></td>
  <td align="center"><select id="maxenergy" style="width:60px" onchange="processETOptions(1)" disabled>
    <option>10</option>
  </select></td>
  <td align="left"><select id="time" style="width:80px" onchange="processETOptions(2)" disabled>
	<option value="12">12m</option>
   </select></td>
  <td align="right" style="width:65px; border-left: 3px double #000;"><input id="curenergy" type="number" value="0" min="0" max="197" style="width:60px"/></td>
  <td><input id="cursapphires" type="number" value="0" min="0" max="250000" style="width:80px"/></td>
  </tr></tbody></table>
   
   </p>
   <p><b>Helpers:</b><br>
   <input id="help50" type="checkbox"> 50 power <input id="help75" type="checkbox"> 75 power</p>
   <p><b>Stage 1</b> completed: <input id="s1" type="checkbox" onchange="checkoption(1)"> or damage dealt: <input id="s0" type="number" value="0" min="0" max="999" style="width:70px"/></p>
   <p><b>Stage 2</b> completed: <input id="s3" type="checkbox" onchange="checkoption(3)"> or damage dealt: <input id="s2" type="number" value="0" min="0" max="9999" style="width:70px" onchange="checkoption(2)"/></p>
   <p><b>Stage 3</b> damage dealt: <input id="s4" type="number" value="0" min="0" max="49999" style="width:70px" onchange="checkoption(4)"/></p>
   
   <h1 id="result" style="width:450px" align="center"></h1>
   
  <p><input style="width:450px" type="button" value="Calculate" onclick="calctime()"></p>
  
  <table><thead align="center"><tr>
  <th colspan="3"></th>
  <td colspan="3" style="width:80px" align="center">Stop upgrading at:</td></tr>
  <tr>
  <th style="vertical-align:bottom width:20px">Extra<br>gems</th>
  <th style="vertical-align:bottom width:60px">Time<br>to reach</th>
  <th style="vertical-align:bottom width:60px">Margin<br>of error</th>
  <th><img name="powerpic" src="" alt="Power" /></th>
  <th><img name="maxenergypic" src="" alt="Max energy" /></th>
  <th><img name="cooldownpic" src="" alt="Cooldown time" /></th>
  </tr></thead>
  <tbody align="center"><tr id="gem50000">
  <td><img name="gempic" src="" alt="Gems" /><b>20</b></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  </tr>
  <tr id="gem25000">
  <td><img name="gempic" src="" alt="Gems" /><b>15</b></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  </tr>
  <tr id="gem10000">
  <td><img name="gempic" src="" alt="Gems" /><b>10</b></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  </tr>
  <tr id="gem5000">
  <td><img name="gempic" src="" alt="Gems" /><b>5</b></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  </tr>
  </tbody>
  </table><br>
  <i>All calculations are approximate, actual times depend on<br>
  your luck with number of sapphires dropping for every hit<br>
  (plus any additional energy and sapphires picked from area)<br>
  You can check the spread of values by switching the option<br>
  below and calculating the result several times.<br>
  Margin of error calculated as 2.5% from average value<br>
  since 90% of spread included in these boundaries</i><br>
  <b>Sapphires per hit:</b>
  <table>
  <td><input id="drop1" name="sappdrop" type="radio" value="avg" checked>Average (60)</td>
  <td><input id="drop2" name="sappdrop" type="radio" value="rand">Random (25-150)</td>
  </table>
</body>
</html>