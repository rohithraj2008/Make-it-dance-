# Make-it-dance-
Take a photo of any object and make it dance for absolutely no reason. 🕺

CODE📓

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">

<title>Object Dance AI</title>

<style>
*{
    box-sizing:border-box;
}

body{
    margin:0;
    background:#09090d;
    color:white;
    font-family:Arial,sans-serif;
    text-align:center;
}

.app{
    width:94%;
    max-width:500px;
    margin:auto;
    padding:25px 0 50px;
}

h1{
    font-size:30px;
    margin:5px 0;
}

.sub{
    color:#aaa;
    line-height:1.5;
}

button{
    width:92%;
    padding:17px;
    margin:8px;
    border:0;
    border-radius:18px;
    font-size:17px;
    font-weight:bold;
}

.green{
    background:#00ff88;
    color:#000;
}

.yellow{
    background:#ffd400;
    color:#000;
}

.pink{
    background:#ff3d81;
    color:white;
}

.red{
    background:#ff4444;
    color:white;
}

.gray{
    background:#444;
    color:white;
}

input{
    display:none;
}

#preview{
    width:100%;
    max-height:350px;
    object-fit:contain;
    border-radius:20px;
    margin-top:15px;
    display:none;
}

#stage{
    display:none;
    width:100%;
    height:430px;
    margin-top:20px;
    border-radius:25px;
    overflow:hidden;
    background:
        radial-gradient(#333 1px,transparent 1px);
    background-size:20px 20px;
}

canvas{
    width:100%;
    height:100%;
}

#status{
    color:#ffd400;
    min-height:25px;
    margin:18px;
}

#report{
    display:none;
    background:#18181f;
    padding:18px;
    border-radius:20px;
    margin-top:20px;
}

.stat{
    display:flex;
    justify-content:space-between;
    margin:14px 0;
}

.greenText{
    color:#00ff88;
}

.big{
    font-size:30px;
    font-weight:bold;
}

</style>
</head>

<body>

<div class="app">

<h1>🕺 OBJECT DANCE AI</h1>

<p class="sub">
Take a photo of anything.<br>
Remove the background.<br>
Then make the <b>OBJECT ITSELF</b> dance! 😂
</p>

<input
    id="camera"
    type="file"
    accept="image/*"
    capture="environment"
>

<button
    class="green"
    onclick="openCamera()">

📷 TAKE OBJECT PHOTO

</button>

<img id="preview">

<p id="status">
📷 Waiting for object...
</p>

<button
    id="processBtn"
    class="yellow"
    style="display:none"
    onclick="processImage()">

✂️ ISOLATE OBJECT

</button>

<div id="stage">
<canvas id="canvas"></canvas>
</div>

<div id="report">

<h2>🤖 USELESS AI REPORT</h2>

<div class="stat">
<span>🧠 Intelligence</span>
<b class="greenText">87%</b>
</div>

<div class="stat">
<span>🕺 Dance Ability</span>
<b class="greenText">99%</b>
</div>

<div class="stat">
<span>🎵 Rhythm</span>
<b class="greenText">91%</b>
</div>

<div class="stat">
<span>😎 Vibes</span>
<b class="greenText">100%</b>
</div>

<hr>

<p>💀 Actual usefulness</p>

<div class="big">0%</div>

<p>There was absolutely no reason to build this. 😂</p>

</div>

<button
    id="danceBtn"
    class="pink"
    style="display:none"
    onclick="startDance()">

🕺 MAKE IT DANCE

</button>

<button
    id="stopBtn"
    class="red"
    style="display:none"
    onclick="stopDance()">

🛑 STOP DANCING

</button>

<button
    id="againBtn"
    class="gray"
    style="display:none"
    onclick="resetApp()">

🔄 TRY ANOTHER OBJECT

</button>

</div>


<script>



const camera =
document.getElementById("camera");

const preview =
document.getElementById("preview");

const canvas =
document.getElementById("canvas");

const ctx =
canvas.getContext("2d");

const status =
document.getElementById("status");

const processBtn =
document.getElementById("processBtn");

const stage =
document.getElementById("stage");

const danceBtn =
document.getElementById("danceBtn");

const stopBtn =
document.getElementById("stopBtn");

const againBtn =
document.getElementById("againBtn");

const report =
document.getElementById("report");

let img = null;

let cutout = null;

let dancing = false;

let animationFrame;



function openCamera(){

    camera.click();

}



camera.addEventListener("change",function(){

    const file = camera.files[0];

    if(!file) return;

    const url =
    URL.createObjectURL(file);

    preview.src = url;

    preview.style.display = "block";

    processBtn.style.display = "block";

    status.innerText =
    "📸 Photo captured!";

});




function loadImage(src){

    return new Promise((resolve,reject)=>{

        const image = new Image();

        image.onload = ()=>{
            resolve(image);
        };

        image.onerror = reject;

        image.src = src;

    });

}




async function processImage(){

    processBtn.style.display = "none";

    status.innerText =
    "🤖 ANALYZING OBJECT...";

    await wait(700);

    status.innerText =
    "✂️ REMOVING BACKGROUND...";

    await wait(700);

    img =
    await loadImage(preview.src);

    createCutout();

}



function createCutout(){

    const temp =
    document.createElement("canvas");

    const maxSize = 700;

    let scale =
    Math.min(
        1,
        maxSize / Math.max(
            img.width,
            img.height
        )
    );

    temp.width =
    Math.floor(img.width * scale);

    temp.height =
    Math.floor(img.height * scale);

    const tctx =
    temp.getContext("2d");

    tctx.drawImage(
        img,
        0,
        0,
        temp.width,
        temp.height
    );

    const imageData =
    tctx.getImageData(
        0,
        0,
        temp.width,
        temp.height
    );

    const data =
    imageData.data;


    /*
       Get background color
       from the four corners.
    */

    const corners = [

        getPixel(data,0,0,temp.width),

        getPixel(
            data,
            temp.width-1,
            0,
            temp.width
        ),

        getPixel(
            data,
            0,
            temp.height-1,
            temp.width
        ),

        getPixel(
            data,
            temp.width-1,
            temp.height-1,
            temp.width
        )

    ];


    const bg = {

        r:average(corners,"r"),

        g:average(corners,"g"),

        b:average(corners,"b")

    };


    /*
       Remove pixels that are similar
       to the background.

       This creates transparency.
    */

    for(
        let y=0;
        y<temp.height;
        y++
    ){

        for(
            let x=0;
            x<temp.width;
            x++
        ){

            const index =
            (y*temp.width+x)*4;

            const r=data[index];

            const g=data[index+1];

            const b=data[index+2];


            const distance =
            Math.sqrt(

                Math.pow(r-bg.r,2)+
                Math.pow(g-bg.g,2)+
                Math.pow(b-bg.b,2)

            );


            /*
               Pixels close to background
               become transparent.
            */

            if(distance < 65){

                data[index+3] = 0;

            }

        }

    }


    tctx.putImageData(
        imageData,
        0,
        0
    );


    cutout = temp;


    status.innerText =
    "✅ OBJECT ISOLATED!";


    stage.style.display =
    "block";

    report.style.display =
    "block";

    danceBtn.style.display =
    "block";

    resizeCanvas();

    drawStill();

}



function getPixel(
    data,
    x,
    y,
    width
){

    const i =
    (y*width+x)*4;

    return {

        r:data[i],

        g:data[i+1],

        b:data[i+2]

    };

}




function average(
    arr,
    key
){

    let total=0;

    arr.forEach(
        item=>{
            total += item[key];
        }
    );

    return total/arr.length;

}



function resizeCanvas(){

    const rect =
    stage.getBoundingClientRect();

    canvas.width =
    rect.width;

    canvas.height =
    rect.height;

}



let objectW;

let objectH;

function drawStill(){

    ctx.clearRect(
        0,
        0,
        canvas.width,
        canvas.height
    );


    const maxW =
    canvas.width*0.65;

    const maxH =
    canvas.height*0.65;


    const scale =
    Math.min(

        maxW/cutout.width,

        maxH/cutout.height

    );


    objectW =
    cutout.width*scale;

    objectH =
    cutout.height*scale;


    const x =
    (canvas.width-objectW)/2;


    const y =
    (canvas.height-objectH)/2;


    ctx.drawImage(

        cutout,

        x,
        y,
        objectW,
        objectH

    );

}



function startDance(){

    dancing=true;

    danceBtn.style.display="none";

    stopBtn.style.display="block";

    againBtn.style.display="block";

    status.innerText =
    "🕺 THE OBJECT IS DANCING!";

    playMusic();

    animate();

}


function animate(){

    if(!dancing) return;


    const time =
    performance.now()/1000;


    ctx.clearRect(
        0,
        0,
        canvas.width,
        canvas.height
    );


    const centerX =
    canvas.width/2;

    const centerY =
    canvas.height/2;


    const startX =
    centerX-objectW/2;

    const startY =
    centerY-objectH/2;


    /*
       MORE SLICES =
       MORE FLEXIBLE OBJECT
    */

    const slices=30;

    const sliceH =
    objectH/slices;


    for(
        let i=0;
        i<slices;
        i++
    ){

        /*
           Position from
           top to bottom.
        */

        const p =
        i/(slices-1);


        /*
           MAIN BODY WAVE
        */

        const wave =
        Math.sin(
            time*7+p*7
        )*18;


        /*
           Each section has
           slightly different movement.
        */

        const wiggle =
        Math.sin(
            time*11+i*0.8
        )*7;


        /*
           Bounce.
        */

        const bounce =
        Math.abs(
            Math.sin(time*4)
        )*25;


        /*
           Bottom moves more,
           like dancing legs.
        */

        const bottomDance =
        p>0.55

        ?

        Math.sin(
            time*10+p*10
        )*20

        :

        0;


        /*
           TOP/BOTTOM MOVEMENT
        */

        const x =
        wave+
        wiggle+
        bottomDance;


        /*
           Bend the object.

           Top and bottom rotate
           differently.
        */

        const rotation =
        Math.sin(
            time*5+p*4
        )*0.13;


        /*
           Squash/stretch.
        */

        const scaleX =
        1+
        Math.sin(
            time*6+p*5
        )*0.10;


        /*
           Save canvas.
        */

        ctx.save();


        /*
           Move to slice position.
        */

        ctx.translate(

            startX+
            objectW/2+
            x,

            startY+
            i*sliceH+
            sliceH/2-
            bounce

        );


        /*
           Rotate THIS PART,
           not the whole object.
        */

        ctx.rotate(rotation);


        /*
           Stretch THIS PART.
        */

        ctx.scale(
            scaleX,
            1
        );


        /*
           Draw one horizontal
           piece of object.
        */

        ctx.drawImage(

            cutout,

            0,

            i*cutout.height/slices,

            cutout.width,

            cutout.height/slices,

            -objectW/2,

            -sliceH/2,

            objectW,

            sliceH+1

        );


        ctx.restore();

    }


    animationFrame =
    requestAnimationFrame(
        animate
    );

}




function stopDance(){

    dancing=false;

    cancelAnimationFrame(
        animationFrame
    );

    stopBtn.style.display="none";

    danceBtn.style.display="block";

    status.innerText =
    "😵 The object needs a break.";

    drawStill();

}



function playMusic(){

    const AudioContext =
    window.AudioContext ||
    window.webkitAudioContext;

    if(!AudioContext) return;


    const audio =
    new AudioContext();


    const notes=[
        220,
        330,
        440,
        330,
        550,
        440,
        660,
        330,
        220
    ];


    let time =
    audio.currentTime;


    notes.forEach(freq=>{

        const osc =
        audio.createOscillator();

        const gain =
        audio.createGain();


        osc.type="square";

        osc.frequency.value=freq;


        osc.connect(gain);

        gain.connect(
            audio.destination
        );


        gain.gain.setValueAtTime(
            0.07,
            time
        );


        gain.gain.exponentialRampToValueAtTime(
            0.001,
            time+0.18
        );


        osc.start(time);

        osc.stop(
            time+0.18
        );


        time+=0.20;

    });

}



function wait(ms){

    return new Promise(
        resolve=>setTimeout(
            resolve,
            ms
        )
    );

}



function resetApp(){

    dancing=false;

    cancelAnimationFrame(
        animationFrame
    );


    preview.style.display="none";

    stage.style.display="none";

    report.style.display="none";

    processBtn.style.display="none";

    danceBtn.style.display="none";

    stopBtn.style.display="none";

    againBtn.style.display="none";


    status.innerText =
    "📷 Waiting for object...";


    camera.value="";

    img=null;

    cutout=null;

}




window.addEventListener(
"resize",
()=>{

    if(stage.style.display!=="none"){

        resizeCanvas();

        if(!dancing){
            drawStill();
        }

    }

});

</script>

</body>
</html>
