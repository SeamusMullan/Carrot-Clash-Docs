# Character Visual Development Guidelines

## Cel-Shading Technique Reference

### Cartoon Outline Implementation
- **Line Weight:** 2-3px thick black outlines on character silhouettes
- **Contextual Variation:** Thicker lines (3-4px) on primary read elements like faces and weapons
- **Inner Line Detail:** 1-2px interior detail lines for important features
- **Depth Technique:** Line weight slightly reduces on elements further from camera
- **Technical Approach:** Post-process edge detection with falloff customization by character

### Color Banding Reference
- **Light Steps:** 3-4 distinct color bands per base color (light to dark)
- **Highlight Style:** Crisp white/bright highlights with minimal feathering
- **Shadow Handling:** Dramatic dark band for shadow areas with minimal gradient
- **Color Vibrancy:** Saturation boost of 15-20% over realistic equivalent
- **Material Variation:** 
  * Root Resistance: Practical, military material bands
  * Blight Brethren: Toxic glow effects between bands
  * Seasoning Syndicate: Metallic sheen bands with sharp contrast
  * Sunshard Council: Soft light emission between bands

## Character Proportion Guide

### Root Resistance Heroes
- **Head-to-Body Ratio:** 1:3 to 1:4 (slightly cartoony but still heroic)
- **Limb Exaggeration:** Arms 110% expected length, legs variable by vegetable type
- **Facial Feature Size:** Eyes 150% realistic proportion, simplified mouth shapes
- **Muscle Definition:** Stylized "heroic vegetable" musculature with simplified forms
- **Gear Scale:** Weapons and equipment 120% expected size for readability

### Blight Brethren Villains
- **Proportion Distortion:** Asymmetrical design with 10-20% variation between sides
- **Mutation Emphasis:** 150-200% scale on mutated elements
- **Facial Corruption:** Partial distortion of facial features with toxic elements
- **Animation Range:** Greater-than-natural joint movement for unsettling effect
- **Gear Integration:** Equipment partially fused with body for disturbing visual

### Seasoning Syndicate Mercenaries
- **Stylish Exaggeration:** Fashion-forward proportions with dramatic posture
- **Accessory Scale:** Oversized status symbol items (weapons, jewelry)
- **Silhouette Drama:** Exaggerated characteristic shapes (peppers more curved, etc.)
- **Expression Range:** Theatrical facial expressions with dramatic emotion states
- **Material Contrast:** High-shine vs. matte surface contrasts for visual pop

### Sunshard Council Members
- **Elongation Factor:** 110-120% height with elegant, stretched proportions
- **Light Integration:** Body parts that emit or channel light effects
- **Ceremonial Scale:** Oversized ceremonial elements and scientific apparatus
- **Wisdom Indicators:** Exaggerated "wise" features (beard-like elements, posture)
- **Tech Harmony:** Seamless blend of natural form with technological elements

## Animation Style Reference

### Key Character Animations
- **Root Resistance:** Military precision with heroic flourishes
  * Crispin: Quick hero poses after key attacks with dramatic follow-through
  * Tater: Comical bouncing with ground-shaking impact frames
  * Sharpshoot: Dramatic zoom effects and precision targeting poses
  * Flora: Ballet-inspired healing movements with flowing particle effects

- **Blight Brethren:** Unsettling, corrupted movements
  * Lord Petrovich: Executive posturing corrupted by sudden twitches
  * Colonel Carnivore: Insect-like movement with unnaturally fast scuttling
  * Wilma: Unsettling flexibility with drooping, fluid motion trails
  * Pilgrim: Puppet-like jerky movements with religious pose holds

- **Seasoning Syndicate:** Stylish, showy combat flourishes
  * Pepper Prince: Gun-kata style with dramatic pirouettes
  * Madame Szechuan: Strategic combat dance with elegant gesture flairs
  * Bullseye: Over-the-top explosive reactions with comic timing
  * Shiv: Ninja-fast strikes with anime speed lines and disappearing effects

- **Sunshard Council:** Ceremonial, light-infused movements
  * Kernelia: Wise, flowing gestures with light trail effects
  * Poddington: Excited scientific gestures with multiple limbs moving
  * Guardian: Disciplined stance shifts with geometric precision
  * Lumina: Youthful energy with bursts of healing light

### Technical Animation Notes
- **Squash & Stretch:** 10-30% deformation range depending on character type
- **Anticipation Frames:** Exaggerated 2-3 frame anticipation before key actions
- **Smear Technique:** Geometry-based smears for fast movements rather than motion blur
- **Secondary Motion:** Exaggerated follow-through on character-specific elements
- **Idle Animations:** Each character should have distinct "personality loop" when idle

## Material & Shader Breakdown

### Root Resistance Materials
- **Carrot Shader:** Orange base with 3 light bands, subtle texture definition
- **Potato Surface:** Brown base with "dirty tactical" weathering effect
- **Plant Top Shader:** Exaggerated leaf movement with hero-green coloration
- **Military Gear:** Slightly weathered with faction symbol highlighting

### Blight Brethren Materials
- **Corruption Shader:** Procedural toxic pattern with subtle animation
- **Glow Effect:** Emission-mapped toxic areas with pulsing intensity
- **Mutation Material:** Bubble-like surface deformation with translucency
- **Toxic Drip Effect:** Stylized drip particles with trail renderer

### Seasoning Syndicate Materials
- **Spice Surface:** High-gloss variation with rich color saturation
- **Metal Accents:** Over-bright specular highlights on luxury items
- **Designer Fabric:** Subtle pattern maps with signature elements
- **Status Indicator:** Material value communicated through shine and pattern

### Sunshard Council Materials
- **Light Channel Shader:** Internal glow with external light emission
- **Ceremonial Cloth:** Flowing fabric with subtle luminescence
- **Purification Effect:** Clean, bright particle systems with minimal noise
- **Scientific Equipment:** Clinical, precise material with data visualization

## Emotional Expression Reference

### Facial Animation Guide
- **Eye Exaggeration:** 200% scale change between neutral and surprised states
- **Mouth Shapes:** Bold, readable mouth positions with comic styling
- **Emotion Indicators:** Environmental effects that amplify emotional state
  * Anger: Steam/heat effects from head area
  * Joy: Sparkle/glow effects around character
  * Fear: Exaggerated shaking with sweat droplets
  * Determination: Dramatic lighting shift on face
- **Combat Expression:** Unique facial combat state for each character