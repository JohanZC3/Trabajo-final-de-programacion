breed [ leaves leaf ]
breed [ dead-leaves dead-leaf ]
breed [ raindrops raindrop ]
breed [ suns sun ]
breed [ snowflakes snowflake ]

leaves-own [
  water-level
  sugar-level
  attachedness
  chlorophyll
  carotene
  anthocyanin
]

raindrops-own [
  location
  amount-of-water
]

globals [
  bottom-line
  evaporation-temp
  season
]

to setup
  clear-all
  set bottom-line min-pycor + 1
  set evaporation-temp 30
  set season "autumn"
  set-default-shape raindrops "circle"
  set-default-shape suns "circle"
  set-default-shape snowflakes "circle"
  set temperature 20
  set sun-intensity 97
  ask patches [
    set pcolor blue - 2
  ]
  ask patches with [ pycor < min-pycor + 2 ] [
    set pcolor green
  ]
  create-leaves number-of-leaves [
    set chlorophyll 50 + random 50
    set water-level 75 + random 25
    set sugar-level random-normal start-sugar-mean start-sugar-stddev
    set carotene random 100
    change-color
    set attachedness 100 + random 50
    fd sqrt random-float 100
  ]
  ask patches with [
    pxcor = 0 and pycor <= 5 or
    abs pxcor = (pycor + 2) and pycor < 4 or
    abs pxcor = (pycor + 8) and pycor < 3
  ] [
    set pcolor brown
  ]
  create-suns 1 [
    setxy (max-pxcor - 2) (max-pycor - 3)
    show-intensity
  ]
  reset-ticks
end

to go
  if not any? leaves and season = "autumn" [
    change-to-winter
  ]
  if sun-intensity = 9 [stop]
  make-wind-blow
  ifelse any? leaves [
    make-rain-fall
  ] [
    make-snow-fall
  ]
  move-water
  ask suns [ show-intensity ]
  ask attached-leaves [
    adjust-water
    adjust-chlorophyll
    adjust-sugar
    change-color
    change-shape
  ]
  ask leaves [ fall-if-necessary ]
  ask leaves with [ ycor <= bottom-line ] [
    set breed dead-leaves
  ]
  ask leaves with [ water-level < 1 ] [
    set attachedness 0
  ]
  ask leaves [
    set chlorophyll (clip chlorophyll)
    set water-level (clip water-level)
    set sugar-level (clip sugar-level)
    set carotene (clip carotene)
    set anthocyanin (clip anthocyanin)
    set attachedness (clip attachedness)
  ]
  if season = "winter" [
    fall-snow
    if sun-intensity >= 10[
      set sun-intensity sun-intensity - 2
    ]
  ]
  tick
end

to-report clip [ value ]
  if value < 0 [ report 0 ]
  if value > 100 [ report 100 ]
  report value
end

to make-wind-blow
  ask leaves [
    ifelse random 2 = 1
      [ rt 10 * wind-factor ]
      [ lt 10 * wind-factor ]
    set attachedness attachedness - wind-factor
  ]
end

to make-rain-fall
  create-raindrops rain-intensity [
    setxy random-xcor max-pycor
    set heading 180
    fd 0.5 - random-float 1.0
    set size .3
    set color gray
    set location "falling"
    set amount-of-water 10
  ]
  ask raindrops [ fd random-float 2 ]
end

to make-snow-fall
  create-raindrops rain-intensity [
    setxy random-xcor max-pycor
    set heading 180
    fd 0.5 - random-float 1.0
    set size .3
    set color white
    set location "falling"
    set amount-of-water 10
  ]
  ask raindrops [ fd random-float 2 ]
end

to move-water
  ask raindrops with [ location = "falling" and pcolor = green ] [
    set location "in roots"
    face patch 0 ycor
  ]
  ask raindrops with [ location = "in roots" and pcolor = brown ] [
    face patch 0 0
    set location "in trunk"
  ]
  ask raindrops with [ location = "in trunk" and patch-here = patch 0 0 ] [
    set location "in leaves"
    set heading random 360
  ]
  ask raindrops with [ location = "in leaves" and amount-of-water <= 0.5 ] [
    die
  ]
  ask raindrops with [
    (location = "in trunk" or location = "in leaves")
    and (ycor > max [ ycor ] of leaves or
         xcor > max [ xcor ] of leaves or
         xcor < min [ xcor ] of leaves)
  ] [
    die
  ]
end

to show-intensity
  set color scale-color yellow sun-intensity 0 150
  set size sun-intensity / 10
  ifelse sun-intensity < 50
    [ set label-color yellow ]
    [ set label-color black  ]
end

to adjust-water
  if temperature < 10 [ stop ]
  let nearby-raindrops raindrops in-radius 2 with [ location = "in leaves" and amount-of-water >= 0 ]
  if any? nearby-raindrops [
    let my-raindrop min-one-of nearby-raindrops [ distance myself ]
    set water-level water-level + ([ amount-of-water ] of my-raindrop * 0.20)
    ask my-raindrop [
      set amount-of-water (amount-of-water * 0.80)
    ]
  ]
  if temperature > evaporation-temp [
    set water-level water-level - (0.5 * (temperature - evaporation-temp))
  ]
  if water-level < 25 [
    set attachedness attachedness - 1
  ]
end

to adjust-chlorophyll
  if temperature < 15 [
    set chlorophyll chlorophyll - (.5 * (15 - temperature))
  ]
  if sun-intensity > 75 [
    set chlorophyll chlorophyll - (.5 * (sun-intensity - 75))
  ]
  if temperature > 15 and sun-intensity > 20 [
    set chlorophyll chlorophyll + 1
  ]
end

to adjust-sugar
  if water-level > 1 and sun-intensity > 20 and chlorophyll > 1 [
    set water-level water-level - 0.5
    set chlorophyll chlorophyll - 0.5
    set sugar-level sugar-level + 1
    set attachedness attachedness + 5
  ]
  set sugar-level sugar-level - 0.5
end

to fall-if-necessary
  if attachedness > 0 [ stop ]
  if ycor > bottom-line [
    let target-xcor (xcor + random-float wind-factor - random-float wind-factor)
    facexy target-xcor bottom-line
    fd random-float (.7 * max (list wind-factor .5))
  ]
end

to change-color
  if temperature < 20 and sugar-level > 0 and water-level > 0 [
    set sugar-level sugar-level - 1
    set water-level water-level - 1
    set anthocyanin anthocyanin + 1
  ]
  ifelse chlorophyll > 50 [
    set color scale-color green chlorophyll 150 -50
  ] [
    if abs (anthocyanin - carotene ) < 10 [
      set color scale-color orange carotene 150 -50
    ]
    if anthocyanin > carotene + 10 [
      set color scale-color red anthocyanin 170 -50
    ]
    if carotene > anthocyanin + 10 [
      set color scale-color yellow carotene 150 -50
    ]
  ]
end

to change-shape
  ifelse leaf-display-mode = "solid" [
    set shape "default"
  ] [
    if leaf-display-mode = "chlorophyll" [
      set-shape-for-value chlorophyll
    ]
    if leaf-display-mode = "water" [
      set-shape-for-value water-level
    ]
    if leaf-display-mode = "sugar" [
      set-shape-for-value sugar-level
    ]
    if leaf-display-mode = "carotene" [
      set-shape-for-value carotene
    ]
    if leaf-display-mode = "anthocyanin" [
      set-shape-for-value anthocyanin
    ]
    if leaf-display-mode = "attachedness" [
      set-shape-for-value attachedness
    ]
  ]
end

to-report attached-leaves
  report leaves with [attachedness > 0]
end

to set-shape-for-value [ value ]
  ifelse value > 75 [
    set shape "default"
  ] [
    ifelse value <= 25 [
      set shape "default one-quarter"
    ] [
      ifelse value <= 50 [
        set shape "default half"
      ] [
        set shape "default three-quarter"
      ]
    ]
  ]
end

to change-to-winter
  set season "winter"
  set temperature 0
  ask patches with [ pycor < min-pycor + 2 ] [
    set pcolor white
  ]
  create-snowflakes 100 [
    setxy random-xcor max-pycor
    set heading 180
    fd 0.5 - random-float 1.0
    set size .3
    set color white
  ]
  ask snowflakes [
    fall-snow
  ]
end

to fall-snow
  ask snowflakes [
    fd random-float 2
    if ycor < bottom-line [
      die
    ]
  ]
end
