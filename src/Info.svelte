<script>
    import {getContext} from 'svelte';

    export let cardList;
    export let equippedList;
    export let calculateFlag;
    export let calculateFlagClickEvent;

    let cardLeftClickEvent = getContext("cardLeftClickEvent");
    let cardRightClickEvent = getContext("cardRightClickEvent");

    function downloadCSV(){
        let cardList = JSON.parse(localStorage.getItem("cardList"));
        let equippedList = JSON.parse(localStorage.getItem("equippedList"));

        for(let key in equippedList){
            cardList[key].equip = true;
        }

        let csv = "이름,등급,보유,각성,장착\n";

        for(let key in cardList){
            let name = cardList[key].name;
            let grade = cardList[key].grade;
            let isHas = cardList[key].isHas;
            let active = cardList[key].active;
            let equip = (cardList[key].equip)?1:0;

            csv += `${name},${grade},${isHas},${active},${equip}\n`
        }

        let hiddenElement = document.createElement("a");

        hiddenElement.href = "data:text/csv;charset=utf-8," + encodeURI("\ufeff" + csv);
        hiddenElement.target = "_blank";
        hiddenElement.download = "backup.csv";
        hiddenElement.click();
    }

    function uploadCSV(){
        let elem = document.getElementById("file");
        let files = elem.files;

        if(validateFiles(files)){
            let reader = new FileReader();
            reader.onload = function (file){
                let data = file.target.result;
                parseData(data);
            }
            reader.readAsText(files[0]);
        }

        elem.value = null;
    }

    function validateFiles(files){
        if(files.length > 1){
            alert("파일은 한개만 적용가능합니다.");
            return false;
        }
        let file = files[0];
        if(!file.type.endsWith("/csv")){
            alert("csv파일만 적용가능합니다.");
            return false;
        }
        return true;
    }

    function parseData(data){
        let rowData = data.split(/\r\n|\n/);

        for(let i in rowData){
            rowData[i] = rowData[i].split(",");

            if(rowData[i].length < 2){
                rowData.splice(i,1);
                i--;
            }
        }

        for(let i in rowData[0]){
            let key = rowData[0][i];
            let value = CSVMap[key];

            if(value === undefined){
                alert("파일오류, 1번째 줄");
                return false;
            }

            rowData[0][i] = value;
        }

        let result = {};

        for(let i = 1; i < rowData.length; i++){
            let tmpObject = {};

            for(let j = 0; j < rowData[i].length; j++){
                tmpObject[rowData[0][j]] = rowData[i][j];
            }

            if(!validate(tmpObject)){
                alert("파일오류, "+(i+1)+"번째 줄");
                return false;
            }

            result[tmpObject["name"]] = tmpObject;
        }

        applyData(result);

        function validate(obj){
            if(cardList[obj["name"]] === undefined){
                return false;
            }

            if(!Number.isInteger(Number(obj["isHas"]))){
                return false;
            }

            if(!Number.isInteger(Number(obj["equip"]))){
                return false;
            }

            if(!Number.isInteger(Number(obj["active"]))){
                return false;
            }

            if(obj["isHas"] > 1 || obj["isHas"] < 0){
                return false;
            }

            if(obj["equip"] > 1 || obj["equip"] < 0){

                return false;
            }

            if(obj["active"] > 5 || obj["active"] < 0){
                return false;
            }

            return true;
        }
    }

    function applyData(result){
        equippedList = [];

        for(let key in result){
            let name = result[key].name;
            let isHas = Number(result[key].isHas);
            let active = Number(result[key].active);
            let equip = Number(result[key].equip);

            cardLeftClickEvent(name, isHas, active);

            if(equip === 1){
                cardRightClickEvent(name);
            }
        }
    }

    function clickEvent(){
        document.getElementById("file").click();
    }
</script>
<div class="info">
    <div class="flex-column">
        <span class="head">*사용방법*</span>
        <span>카드장착: 장착할 카드 우클릭</span>
        <span>카드장착해제: 장착한 카드 우클릭</span>
        <span>각성단계증가: 카드이름 좌클릭, 해당 각성단계 좌클릭</span>
    </div>
    <div class="btn-area">
        <button class="btn-info" class:active={calculateFlag === 1} on:click={(event) => calculateFlagClickEvent()}>남은 카드 경험치 효율 보기</button>
        <button class="btn-info" on:click={(event) => downloadCSV()}>백업파일 다운로드</button>
        <button id="upload" class="btn-info" on:click={(event) => clickEvent()}>백업파일 적용하기</button>
        <input type="file" id="file" class="file" accept=".csv" on:change={(event) => uploadCSV()}>
    </div>
</div>
<style>
    .info{
        padding: 8px;
        display: flex;
        justify-content: space-between;
        font-weight: 500;
        color: #b8b8b8;
    }
    .flex-column{
        display: flex;
        flex-direction: column;
    }
    .head{
        font-weight: bold;
    }
    .btn-area{
        display: flex;
        flex-direction: column;
        gap: 4px;
    }
    .btn-info{
        height: 32px;
        margin: 0;
        padding: 4px;
        font-weight: 500;
        background: var(--inactive);
        border: 1px solid var(--border);
        cursor: pointer;
    }
    .btn-info.active{
        background: var(--active);
    }
    .file{
        display: none;
    }
</style>