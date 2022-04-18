<script>
	import {setContext} from 'svelte';
	import * as database from '../public/database.json';
	import Info from './Info.svelte';
	import Sort from './Sort.svelte';
	import Board from './Board.svelte';
	import Card from './Card.svelte';
	import GradeBoard from "./GradeBoard.svelte";
	import CollectBoard from "./CollectBoard.svelte";
	import Header from './Header.svelte';
	import Equip from './Equip.svelte';
	import Collect from './Collect.svelte';
	import Footer from './Footer.svelte';

	let localSaved = localStorage.getItem("localSaved");
	let cardList = JSON.parse(JSON.stringify(database.card));
	let equippedObject = (localSaved === "1") ? JSON.parse(localStorage.getItem("equippedObject")) : {};
	let equippedList = (localSaved === "1") ? JSON.parse(localStorage.getItem("equippedList")) : {};
	let equippedEffect = [];
	let collectedObject = (localSaved === "1") ? JSON.parse(localStorage.getItem("collectedObject")) : {};
	let collectedEffect = [];

	let groupFlag = 1;
	let gradeFilter = {
		"일반": 0,
		"고급": 0,
		"희귀": 0,
		"영웅": 0,
		"전설": 0
	};
	let kindFilter = {
		"힘": 0,
		"민첩": 0,
		"지능": 0,
		"체력": 0,
		"치명": 0,
		"특화": 0,
		"제압": 0,
		"신속": 0,
		"인내": 0,
		"숙련": 0,
		"물리 방어력": 0,
		"마법 방어력": 0,
		"인간": 0,
		"악마": 0,
		"물질": 0,
		"불사": 0,
		"식물": 0,
		"곤충": 0,
		"야수": 0,
		"기계": 0
	};

	for (let name in cardList) {
		cardList[name] = {
			"name": name,
			"grade": cardList[name],
			"isHas": 0,
			"active": 0
		}
	}

	if(localSaved === "1") {
		Object.assign(cardList, JSON.parse(localStorage.getItem("cardList")));
	}

	//localStorage저장
	$: {
		localStorage.setItem("localSaved", "1");
		localStorage.setItem("cardList", JSON.stringify(cardList));
		localStorage.setItem("equippedObject", JSON.stringify(equippedObject));
		localStorage.setItem("equippedList", JSON.stringify(equippedList));
		localStorage.setItem("collectedObject", JSON.stringify(collectedObject));
	}

	//장착카드 목록 계산
	$: {
		equippedObject = {};
		for(let name in equippedList){
			let equipped = database.equipJoin[name];
			if(equipped !== undefined){
				for (let equip of equipped) {
					//카드 정보 추가
					if (equippedObject[equip] === undefined) {
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
			if(sumObject[key] !== undefined){
				collectedEffect.push([key, sumObject[key]]);
			}
		}
	}

	let renderCardList;

	$:{
		renderCardList = {};

		for(let name in cardList){
			if(checkGrade(cardList[name]) && checkKind(cardList[name])){
				renderCardList[name] = cardList[name];
			}
		}

		function checkGrade(card){
			let sum = 0;

			for(let grade in gradeFilter){
				sum += gradeFilter[grade];
			}

			if(sum === 0){
				return true;
			}

			return gradeFilter[card.grade] === 1;
		}

		function checkKind(card){
			let sum = 0;

			for(let kind in kindFilter){
				sum += kindFilter[kind];
			}

			if(sum === 0){
				return true;
			}

			let collectList = database.collectJoin[card.name];

			if(collectList) {
				for (let i = 0; i < collectList.length; i++) {
					let effectList = database.collect[collectList[i]];
					for (let effect of effectList) {
						if (kindFilter[effect[3]] === 1) {
							return true;
						}
					}
				}
			}

			return false;
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
				"grade": cardList[name],
				"isHas": 0,
				"active": 0
			}
		}
	}

	function cardLeftClickEvent(name, isHas, active){
		//카드가 장착되어 있으면 isHas는 1
		if(equippedList[name] !== undefined){
			isHas = 1;
		}
		let collectList = database.collectJoin[name];
		if(collectList !== undefined){
			for (let collect of collectList) {
				if(isHas === cardList[name].isHas){
					//카드 정보 변경
					if(isHas === 1){
						collectedObject[collect][2] += active - cardList[name].active;
					}
				} else if (isHas === 1) {
					//카드 정보 추가
					if (collectedObject[collect] === undefined) {
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
					"grade": database.card[name],
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

	function groupFlagClickEvent(flag){
		groupFlag = flag;
	}

	function gradeFilterClickEvent(name){
		gradeFilter[name] ^= 1;
	}

	function kindFilterClickEvent(name){
		kindFilter[name] ^= 1;
	}

	function getCollectList(name){
		return database.collectJoin[name];
	}

	setContext("cardLeftClickEvent",cardLeftClickEvent);
	setContext("cardRightClickEvent",cardRightClickEvent);
</script>
<Header/>
<Info/>
<Sort {groupFlag} {gradeFilter} {kindFilter} {groupFlagClickEvent} {gradeFilterClickEvent} {kindFilterClickEvent}/>
<div class="main" style="">
	{#if groupFlag === 0}
		<Board>
			{#each Object.entries(renderCardList) as [name, info]}
				<Card {info}/>
			{/each}
		</Board>
	{:else if groupFlag === 1}
		<GradeBoard {renderCardList}/>
	{:else}
		<CollectBoard {renderCardList} {getCollectList}/>
	{/if}
	<div class="right-area">
		<Equip {equippedList} {equippedEffect} {removeEquippedEvent}/>
		<Collect {collectedEffect}/>
	</div>
</div>
<Footer/>
<style>
	.main{
		display: flex;
	}
	.right-area{
		width: 400px;
		height: 800px;
		flex-shrink: 0;
		display: flex;
		flex-direction: column;
		gap: 20px;
		position: sticky;
		top: 100px;
		right: 0;
	}
	@media (max-width: 800px) {
		.main{
			flex-direction: column;
			gap: 20px;
		}
		.right-area{
			height: auto;
			position: relative;
			top: 0;
		}
	}
</style>