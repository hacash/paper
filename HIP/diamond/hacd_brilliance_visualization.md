
<pre>
HIP: 8
Title: HACD Brilliance Visualization 
Author: Jojoin
Created: 2022-11-01
</pre>

# HACD Brilliance Visualization

In order to further increase the artistry and collectability of HACD diamonds, a visualization proposal of "HACD Brilliance" inspired by the brilliance level check of physical diamonds is proposed. The idea is also inspired by artistically beautiful objects such as nebulae, snowflakes, pupils, stamens, kaleidoscopes, etc.

This visualization proposal simulates the luster effect of a physical diamond seen through a high magnifying glass under light. The final "gloss gene" is obtained by performing multiple calculations of the SHA3_256 hash algorithm on the original HACD visualization gene, and the microscopic halo effect of the HACD is rendered by SVG rendering.

Due to the unpredictability of the visualized genes prior to HACD confirmation, the resulting calculated gloss genes are also unpredictable. The randomness of the SHA3_256 hash algorithm will allow the glossiness of HACD to exhibit endless variations.

This visualization algorithm uses the original color of HACD for drawing, and the background color, mixed color and main color are all in one-to-one correspondence. 

The visualization code:

```js

// canvas wide = 1000px
function NewArtForHACD(vgene, backcolor) {
    vgene = vgene.toLowerCase()
    backcolor = backcolor || 'black' // background color

    // HIP-5 colors
    var colors = {
        '0': ['#041B2D', '#004E9A'], 
        '1': ['#004E9A', '#428CD4'], 
        '2': ['#8A5082', '#6F5F90'], 
        '3': ['#6F5F90', '#758EB7'], 
        '4': ['#8A5082', '#FF7B89'], 
        '5': ['#FF7B89', '#A5CAD2'], 
        '6': ['#F7D6E0', '#F2B5D4'], 
        '7': ['#E5C1CD', '#C9BBC8'], 
        '8': ['#EFF7F6', '#F7D6E0'], 
        '9': ['#F3DBCF', '#AAC9CE'], 
        'a': ['#AAC9CE', '#B6B4C2'], 
        'b': ['#F2B5D4', '#7BDFF2'], 
        'c': ['#7BDFF2', '#B2F7EF'], 
        'd': ['#85CBCC', '#A7D676'], 
        'e': ['#daad7b', '#F9E2AE'], 
        'f': ['#F9E2AE', '#A8DEE0'],
    }


    // art params
    var cidx = 0
    , artcells = []
    , ty = "UINT8ARRAY"
    , newrets = SHA3_256_UINT8(vgene)
    // , newrets = SHA3_256_UINT8(vgene + new Date().getTime()) // test
    for(var i=0;i<64;i++) {
        for(var k=0;k<32;k++) {
            artcells.push(newrets[k])
        }
        newrets = SHA3_256(newrets, ty, ty)
    }
    function seekrand(max, base) {
        var v = artcells[cidx]
        cidx++
        return v % max + (base||0)
    }
    // console.log(artcells)
    // return SHA3_256_UINT8(vgene)

    var svgobjs = ''

    // background
    svgobjs += `<polygon points="0,0 1200,0 1200,1200 0,1200" fill="black" ></polygon>`

    // draw 4 background colors
    var stuff1 = [
        [3,3,2,8,8,2, vgene[14]],
        [9,3,2,2,8,8, vgene[15]],
        [9,9,8,2,2,8, vgene[16]],
        [3,9,8,8,2,2, vgene[17]],
    ]
    for(var i in stuff1){
        var x = stuff1[i]
        , cl = colors[x[6]]
        svgobjs += `<linearGradient id="grad1_${i}" x1="${x[2]}0%" y1="${x[3]}0%" x2="${x[4]}0%" y2="${x[5]}0%">
        <stop offset="0%" style="stop-color:${cl[0]};stop-opacity:${seekrand(60)/100+0.2}" />
        <stop offset="100%" style="stop-color:${cl[1]};stop-opacity:${seekrand(60)/100+0.2}" />
      </linearGradient>
      <circle style="filter: blur(${seekrand(60, 60)}px); " cx="${x[0]}00" cy="${x[1]}00" r="${seekrand(250, 100)}" fill="url(#grad1_${i})"   />`
    }

    // rotate 8 times
    var opacitys = [seekrand(40)/100+0.1, seekrand(40)/100+0.1, seekrand(20)/100+0.08, seekrand(40)/100+0.2]
    , blurs = [seekrand(40)/10, seekrand(60)/10+2, seekrand(100)/10+4, seekrand(50)/10+1, ]
    , mvpt0s = [600-seekrand(100), seekrand(100), 300+seekrand(200), 600-seekrand(200)]
    , mvpt1s = [600-seekrand(100), seekrand(100), 300+seekrand(200), 600-seekrand(200)]
    , mvpt1sc = [600-seekrand(100), seekrand(100), 300+seekrand(200), 600-seekrand(200)]
    , mvpt2s = [100+seekrand(200), 100+seekrand(300), seekrand(220), 120+seekrand(120) , seekrand(120), 240+seekrand(200)]
    , mvpt3s = [120+seekrand(200), 200+seekrand(200), seekrand(160), 140+seekrand(100)]
    // main color
    , maincols = colors[vgene[2]]
    // points
    , tangpts = [
        {x:550,y:100}, // 0
        {x:380,y:70}, // 1
        {x:294,y:206}, // 2
        {x:415.7,y:155}, // 3
        {x:446,y:228}, // 4
        {x:652,y:57}, // 5
        {x:600,y:300}, // 6
        {x:388,y:388}, // 7
        {x:600,y:100}, // 8
        {x:754,y:228}, // 9
        {x:505,y:369}, // 10
        {x:695,y:369}, // 11
    ]
    // 
    , tangents = [
        // [0,1,2], //
        [3,4,5], true,
        [4,6,10,7],
        [8,4,6,9],
        [6,10,11]
    ]
    // main 
    , maintgpts = [
        {x:505,y:369},
        {x:695,y:369},
        {x:831,y:505}, //2
        {x:831,y:696},
        {x:695,y:831}, //4
        {x:505,y:831},
        {x:369,y:696}, //6
        {x:369,y:505},
    ]
    // 
    , neitgpts = [
        []
    ]

    function gtptw(i, fx) {
        var points = []
        , xs = tangents[i]
        for(var k in xs){
            var p = tangpts[xs[k]]
            , x = fx ? (600+(600-p.x)) : p.x
            points.push(x+','+p.y)
        }
        return points.join(' ')
    }


    for(var i=0; i<8; i++) {
        svgobjs += `<g style="transform: rotate(${45*i}deg); transform-origin: center;">`
        // test
        // svgobjs += `<polygon points="393,100 600,600 807,100" fill="none" style="stroke:purple;stroke-width:1"/>`
      
        // draw 5 light
        
        svgobjs += `<g style="transform: rotate(22.5deg);transform-origin: center;">`

        svgobjs += `<polygon points="600,${mvpt0s[0]} ${600-mvpt0s[1]},${mvpt0s[2]} 600,${mvpt0s[3]} ${600+mvpt0s[1]},${300+seekrand(300)}" opacity="${seekrand(40)/100+0.1}" fill="#fff" style=" filter: blur(${seekrand(80)/10+4}px);"/>`

        svgobjs += `<polygon points="600,${mvpt1s[0]} ${600-mvpt1s[1]},${mvpt1s[2]} 600,${mvpt1s[3]} ${600+mvpt1s[1]},${mvpt1s[2]}" opacity="${seekrand(40)/100+0.1}" fill="#fff" style=" filter: blur(${blurs[0]}px);"/>`

        svgobjs += `<polygon points="600,${mvpt1sc[0]} ${600-mvpt1sc[1]},${mvpt1sc[2]} 600,${mvpt1sc[3]} ${600+mvpt1sc[1]},${mvpt1sc[2]}" opacity="${opacitys[1]}" fill="#fff" style=" filter: blur(${blurs[1]}px);"/>`
        
        svgobjs += `<polygon points="600,${mvpt2s[0]} ${600-mvpt2s[2]},${mvpt2s[3]} ${600-mvpt2s[4]},${mvpt2s[5]} 600,${mvpt2s[1]} ${600+mvpt2s[2]},${mvpt2s[3]}" opacity="${opacitys[2]}" fill="#fff" style="fill-rule:nonzero; filter: blur(${blurs[2]}px);"/>`

        svgobjs += `<polygon points="600,${mvpt3s[0]} ${600-mvpt3s[2]},${mvpt3s[3]}  600,${mvpt3s[1]} ${600+mvpt3s[2]},${mvpt3s[3]}" opacity="${opacitys[3]}" fill="#fff" style="fill-rule:nonzero; filter: blur(${blurs[3]}px);"/>`
        svgobjs += `</g>`
        

        // draw color
        
        var khcol = colors[vgene[3+i]]
        svgobjs += `<linearGradient id="grad3_${i}" x1="${seekrand(100)}%" y1="${seekrand(100)}%" x2="${seekrand(100)}%" y2="${seekrand(100)}%">
        <stop offset="0%"   style="stop-color:${khcol[0]};stop-opacity:${seekrand(70)/100}" />
        <stop offset="100%" style="stop-color:${khcol[1]};stop-opacity:${seekrand(70)/100}" />
        </linearGradient>`
        for(var h=0;h<4;h++){
            var pt3s = []
            for(var c=0; c<4; c++) { pt3s.push(`${400+(h*80)+seekrand(100)},${200+(h*40)+seekrand(100)}`) }
            svgobjs += `<polygon points="${pt3s.join(' ')}" opacity="${seekrand(60)/100+0.2}" fill="url(#grad3_${i})" style="filter: blur(${seekrand(160)/10+4}px);"/>`
            var pt3s1 = []
            for(var c=0; c<4; c++) { pt3s1.push(`${400+h*60+seekrand(200)},${200+h*20+seekrand(200)}`) }
            svgobjs += `<polygon points="${pt3s1.join(' ')}" opacity="${seekrand(60)/100+0.2}" fill="url(#grad3_${i})" style="filter: blur(${seekrand(50)/10+4}px);"/>`
        }
        
        

        // draw face
        for(var j in tangents){
            var a = tangents[j]
            svgobjs += `<linearGradient id="grad2_${i}_${j}" x1="${seekrand(100)}%" y1="${seekrand(100)}%" x2="${seekrand(100)}%" y2="${seekrand(100)}%">
            <stop offset="0%"   style="stop-color:${maincols[0]};stop-opacity:${seekrand(80)/100+0.2}" />
            <stop offset="100%" style="stop-color:${maincols[1]};stop-opacity:${seekrand(80)/100+0.2}" />
            </linearGradient>`
            var pts = a===true ? gtptw(j-1, true) : gtptw(j);
            svgobjs += `<polygon style="filter: blur(${seekrand(40)/10}px);" points="${pts}" opacity="${seekrand(80)/100+0.2}" fill="url(#grad2_${i}_${j})" style=""/>`
        }

        // group end
        svgobjs += `</g>`
    }

    for(var i=0; i<8; i++) {
        svgobjs += `<polygon  points="582,87 249,225 367,43" fill="${backcolor}" style="transform: rotate(${45*i}deg); transform-origin: center; z-index:1;" ></polygon>`
    }

    // main light
    svgobjs += `<linearGradient id="grad4" x1="${seekrand(50)+5}%" y1="${seekrand(50)+5}%" x2="${seekrand(50)+5}%" y2="${seekrand(50)+5}%">
        <stop offset="0%"   style="stop-color:${maincols[0]};stop-opacity:${seekrand(100)/100}" />
        <stop offset="100%" style="stop-color:${maincols[1]};stop-opacity:${seekrand(100)/100}" />
        </linearGradient>`
    var pts=[]
    for(var m in maintgpts) {
        var p=maintgpts[m]
        pts.push(`${p.x},${p.y}`)
    }
    svgobjs += `<polygon style="filter: blur(${seekrand(30)/10+1}px);"  points="${pts.join(' ')}" opacity="${seekrand(70)/100}" fill="url(#grad4)" style=""/>`


        
    // console.log('use cidx: '+cidx)

    // light: 11-99
    var lights = [seekrand(9)+'', seekrand(9)+'']
    lights = [lights[0]+lights[0], lights[1]+lights[1]]
    


    // ok mask
    return `<svg id="artimg" version="1.2" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1200 1200" width="1200" height="1200" style="background-image: linear-gradient(to top, ${maincols[0]}${lights[0]}, ${maincols[1]}${lights[1]})"><g>${svgobjs}<circle cx="600" cy="600" r="700" stroke="${backcolor}" stroke-width="400" fill="none" /></g></svg>`


}




```

