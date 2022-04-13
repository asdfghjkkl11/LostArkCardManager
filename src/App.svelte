<script>
	import * as database from '../public/database.json';
	import Card from './Card.svelte';
	import Header from './Header.svelte';
	import Equip from './Equip.svelte';
	import Collect from './Collect.svelte';
	import Footer from './Footer.svelte';

	let localSaved = localStorage.getItem("localSaved");
	let cardList = (localSaved=="1")?JSON.parse(localStorage.getItem("cardList")):JSON.parse(JSON.stringify(database.card));
	let equippedObject = (localSaved=="1")?JSON.parse(localStorage.getItem("equippedObject")):{};
	let equippedList = (localSaved=="1")?JSON.parse(localStorage.getItem("equippedList")):{};
	let equippedEffect = (localSaved=="1")?JSON.parse(localStorage.getItem("equippedEffect")):[];
	let collectedObject = (localSaved=="1")?JSON.parse(localStorage.getItem("collectedObject")):{};
	let collectedEffect = (localSaved=="1")?JSON.parse(localStorage.getItem("collectedEffect")):[];

	if(localSaved != "1") {
		for (let name in cardList) {
			cardList[name] = {
				"name": name,
				"rarity": cardList[name],
				"isHas": 0,
				"active": 0
			}
		}
	}

	//localStorage저장
	$: {
		localStorage.setItem("localSaved","1");
		localStorage.setItem("cardList",JSON.stringify(cardList));
		localStorage.setItem("equippedObject",JSON.stringify(equippedObject));
		localStorage.setItem("equippedList",JSON.stringify(equippedList));
		localStorage.setItem("equippedEffect",JSON.stringify(equippedEffect));
		localStorage.setItem("collectedObject",JSON.stringify(collectedObject));
		localStorage.setItem("collectedEffect",JSON.stringify(collectedEffect));
	}

	//장착카드 목록 계산
	$: {
		equippedObject = {};
		for(let name in equippedList){
			let equipped = database.equipJoin[name];
			if(equipped !== undefined) {
				for (let equip of equipped) {
					//카드 정보 추가
					if (equippedObject[equip] == undefined) {
						equippedObject[equip] = [equip, 0, 0];
					}
					equippedObject[equip][1] += 1;
					equippedObject[equip][2] += equippedList[name].active;
				}

			}else{
				console.log(name);
			}
		}

		let sumObject = [];

		for(let key in equippedObject){
			let data = database.equip[key];
			for(let equip of data){
				if(equip[1] <= equippedObject[key][1] && equip[2] <= equippedObject[key][2]){
					sumObject.push(equip.concat(true));
				}else{
					sumObject.push(equip.concat(false));
				}
			}
		}
		equippedEffect = sumObject;
	}

	//수집카드 목록 계산
	$: {
		let sumObject = {};
		for(let key in collectedObject){
			let data = database.collect[key];
			for(let collect of data){
				if(collect[1] <= collectedObject[key][1] && collect[2] <= collectedObject[key][2]){
					if(sumObject[collect[3]] === undefined){
						sumObject[collect[3]] = 0;
					}
					sumObject[collect[3]] += Number(collect[4]);
				}
			}
		}
		collectedEffect = [];
		for(let key in collectMap){
			if(sumObject[key] !== undefined) {
				collectedEffect.push([key, sumObject[key]]);
			}
		}
	}

	function init(){
		cardList = JSON.parse(JSON.stringify(database.card));
		equippedObject = {};
		equippedList = {};
		equippedEffect = [];
		collectedObject = {};
		collectedEffect = [];

		for(let name in cardList){
			cardList[name] = {
				"name": name,
				"rarity": cardList[name],
				"isHas": 0,
				"active": 0
			}
		}
	}

	function cardLeftClickEvent(name,isHas,active){
		//카드가 장착되어 있으면 isHas는 1
		if(equippedList[name] !== undefined){
			isHas = 1;
		}
		let collectList = database.collectJoin[name];
		if(collectList !== undefined) {
			for (let collect of collectList) {
				if (isHas === cardList[name].isHas) {
					//카드 정보 변경
					if (isHas === 1) {
						collectedObject[collect][2] += active - cardList[name].active;
					}
				} else if (isHas === 1) {
					//카드 정보 추가
					if (collectedObject[collect] == undefined) {
						collectedObject[collect] = [collect, 0, 0];
					}
					collectedObject[collect][1] += 1;
					collectedObject[collect][2] += active;
				} else {
					//카드 정보 삭제
					collectedObject[collect][1] -= 1;
					collectedObject[collect][2] -= cardList[name].active;

					if (collectedObject[collect][1] < 1 && collectedObject[collect][2] < 1) {
						delete collectedObject[collect];
					}
				}
			}
		}else{
			console.log(name);
		}

		cardList[name].isHas = isHas;
		cardList[name].active = active;

		//장착정보도 수정
		if(equippedList[name] !== undefined) {
			equippedList[name].isHas = isHas;
			equippedList[name].active = active;
		}
	}

	function cardRightClickEvent(name){
		//가지고 있지 않으면 수집처리
		if(cardList[name].isHas === 0){
			cardLeftClickEvent(name,1,cardList[name].active);
		}
		//장착안된 카드만
		if(equippedList[name] === undefined) {
			let equippedCardList = Object.keys(equippedList);
			//최대카드는 6장
			if(equippedCardList.length < 6){
				equippedList[name] = {
					"name": name,
					"rarity": database.card[name],
					"isHas": cardList[name].isHas,
					"active": cardList[name].active
				}
			}
		}
	}

	function removeEquippedEvent(name){
		//장착된 카드만
		if(equippedList[name] !== undefined){
			//delete는 변수값이 바뀐걸로 취급안함
			equippedList[name] = null;
			delete equippedList[name];
		}
	}
</script>
<Header/>
<div style="display: flex">
<div class="board">
	{#each Object.entries(cardList) as [name, info]}
		<Card {info} {cardLeftClickEvent} {cardRightClickEvent}/>
	{/each}
</div>
<div class="right-area">
	<Equip {equippedList} {equippedEffect} {removeEquippedEvent}/>
	<Collect {collectedEffect}/>
</div>
</div>
<Footer/>
<style>
	.board{
		column-width: 340px;
		overflow: auto;
	}
	.right-area{
		width: 400px;
		height: 800px;
		padding-right: 16px;
		flex-shrink: 0;
		display: flex;
		flex-direction: column;
		gap: 20px;
		position: sticky;
		top: 100px;
		right: 0;
	}
</style>