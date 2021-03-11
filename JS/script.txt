//variables

let card = document.getElementsByClassName("card");
let cards = [...card];

let item = document.getElementsByClassName("item");
let items = [...item];

const deck = document.getElementById("card-deck");
var listOfItems = document.getElementById("list-of-items");

let trials = 0;
let trialsCounter = document.querySelector(".trial-counter");

let time = 0;
let timeCounter = document.querySelector(".time-counter");

let success = 0;
let successCounter = document.querySelector(".success-counter");

let matchedCard = document.getElementsByClassName("match");

var openedCards = [];

heading = document.querySelector("h1");

//suffling array of cards
function shuffle(array) {
    var currentIndex = array.length, tmp, randomIndex;

    while (currentIndex !== 0) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex -= 1;
        tmp = array[currentIndex];
        array[currentIndex] = array[randomIndex];
        array[randomIndex] = tmp;
    }

    return array;
};

//clearing variables
function prepareData(){
    openedCards = [];
    time = 0;
    trials = 0;
    second = 0;
    minute = 0; 
    trialsCounter.innerHTML = "Trials <br/> 0";
    timeCounter.innerHTML = "Time <br /> 0";
    successCounter.innerHTML = "Success <br /> 0"; 
}

//hiding names of items
function hideList(){
    for(var i =0;i<items.length;i++) {
        items[i].style.visibility = "hidden";
    }   
}

//removing existing classes from cards
function removeClasses(array){
    for (var i = 0; i < array.length; i++){
        deck.innerHTML = "";
        [].forEach.call(cards, function(item) {
            deck.appendChild(item);
        });
        array[i].classList.remove("show", "open", "match", "disabled");
    }
}

//communication with user
function changeHeader(text){
    heading.innerHTML = text;
}

//start program
function start(){
    prepareData();
    hideList();
    cards = shuffle(cards);
    removeClasses(cards);
    clearInterval(interval);
    let name = prompt("Welcome to Memory Game! What is your name?");
    if(name === null){
        name = "";
    }
    changeHeader(`Good luck ${name}!`);
}


//toggles open and show class to display cards
var displayCard = function (){
    this.classList.toggle("open");
    this.classList.toggle("show");
    this.classList.toggle("disabled");
};

//changing visibility items' names
function showItem(array, type){
    for(var i =0;i<array.length;i++) {
        if(array[i].type === type){
            array[i].style.visibility = "visible";
		}
    }
}

//counting number of success
function countSuccess(){
    success++;
    successCounter.innerHTML = "Success <br />" + success;
}


//add opened cards to OpenedCards list and check if cards are match or not
function cardOpen() {
    openedCards.push(this);
    var len = openedCards.length;
    if(len === 2){
        countTrials();
        if(openedCards[0].type === openedCards[1].type){
            let type = openedCards[0].type;
            showItem(items, type);
            changeHeader(`Well done!`);
            countSuccess();
            matched();
        } else {
            changeHeader(`Try again!`);
            unmatched();
        }
    }

    if (matchedCard.length == 16){
        changeHeader(`Congratulations!`);
        clearInterval(interval);
    }
};


//when cards match
function matched(){
    openedCards[0].classList.add("match", "disabled");
    openedCards[1].classList.add("match", "disabled");
    openedCards[0].classList.remove("show", "open", "no-event");
    openedCards[1].classList.remove("show", "open", "no-event");
    openedCards = [];
}


//when cards don't match
function unmatched(){
    openedCards[0].classList.add("unmatched");
    openedCards[1].classList.add("unmatched");
    disable();
    setTimeout(function(){
        openedCards[0].classList.remove("show", "open", "no-event","unmatched");
        openedCards[1].classList.remove("show", "open", "no-event","unmatched");
        enable();
        openedCards = [];
    },1100);
}


//disable cards temporarily
function disable(){
    Array.prototype.filter.call(cards, function(card){
        card.classList.add('disabled');
    });
}


//enable cards and disable matched cards
function enable(){
    Array.prototype.filter.call(cards, function(card){
        card.classList.remove('disabled');
        for(var i = 0; i < matchedCard.length; i++){
            matchedCard[i].classList.add("disabled");
        }
    });
}

//changing number of trials
function changeTrialsCounter(text){
    trialsCounter.innerHTML = text;
}

//check if time is to start
function checkTrials(moves){
    if(moves == 1){
        second = 0;
        minute = 0;
        return true;
    }

    else return false;
}

//count player's moves
function countTrials(){
    trials++;
    changeTrialsCounter(`Trials <br /> ${trials}`);
    let startTime = checkTrials(trials);
    if(startTime){
        countTime();
    }
}

//changing time
function changeTimeCounter(text){
    timeCounter.innerHTML = text;
}

//game timer
var second = 0, minute = 0; 
var interval;
function countTime(){
    interval = setInterval(function(){
        changeTimeCounter(`Time <br /> ${minute}min ${second}sec`);
        second++;
        if(second == 60){
            minute++;
            second=0;
        }
    },1000);
}

function reloadPage() {
    window.setInterval(location.reload(true), s);
}

//add action to button
let button = document.querySelector("button");
button.addEventListener("click", reloadPage);

//start when page is being loaded
document.body.onload = start();

//add actions to cards
for (var i = 0; i < cards.length; i++){
    card = cards[i];
    card.addEventListener("click", displayCard);
    card.addEventListener("click", cardOpen);
};