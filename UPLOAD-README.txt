NAMMA AUTO - STATIC WEB GAME

An arcade game, not a real-vehicle replica or engineering simulation.

Upload/extract the complete archive, keeping assets/ beside index.html at its
root. Prebuilt static game: no npm install, backend, database, web worker or API
key required. Serve over HTTPS or localhost HTTP, not file://. Relative asset
URLs support nested hosting. Retain THIRD-PARTY-NOTICES.txt and font notices.

Requires a desktop browser with WebGL 2, hardware acceleration and a keyboard.
Permit JavaScript and WebGL when embedding. OPEN GAME loads 3D after the static
preview. START MISSION starts directly: no username/name prompt or required setup.
Anna (male driver, ♂) is the default. Optional home-screen Change Driver selects
Anna or Akka (female driver, ♀); SAVE DRIVER updates the actual model and preview
without starting a mission. Starting unlocks audio; retries reuse the session-only
driver choice. Optional personal-best storage contains only the score.
No analytics or accounts. A BANGALORE DRIVING EXPERIENCE sits beneath the large
NAMMA AUTO title; the small n↗ header menu button remains icon-only.

W/Up: accelerate. A/D or Left/Right: steer. On supported upright ground, S/Down
smoothly brakes forward travel at 16m/s² dry / 14.4m/s² wet, then HELD S REVERSES
on the next 60Hz PHYSICS tick after reaching rest, not within the stopping tick.
Reverse requests 3.5m/s² acceleration toward a 4.5m/s (16.2km/h) governor target.
W/Up brakes reverse travel before driving forward on the next tick after rest.
Space is stronger: 20m/s² dry / 18m/s² wet, then HOLDS STOPPED. Space overrides
both drive keys; S takes priority over W. Grounded idle hold uses a 0.001m/s
rest threshold. The support/crash guards
preserve airborne motion, gravity, rotation and meaningful impact momentum,
including external backward pushes. This is not a world-Z clamp: forward driving
after a U-turn can travel along -Z. HUD shows BRAKE while braking and R for reverse
travel. The R key remains recovery (-100 points, no repair), not a gear selector.
If no safe gap is available, the hint says "Hold SPACE and wait for traffic to
clear". Space holds stopped; held S now reverses after stopping.
C: LOW CHASE -> ELEVATED -> COCKPIT -> LOW CHASE during play.
H: horn. M: mute. Escape: pause. Losing focus pauses play.
Weather and camera labels sit TOP-LEFT. Mission, traffic queue/signal information,
points pop-ups and driving hints remain in the RIGHT-EDGE stack, not the road center.
Messages wrap without overlapping each other. Score/condition remain bottom-left
and speed bottom-right; short windows use a compact speed readout.
Reverse is powered and soft-governed, not a hard velocity clamp.
Deliver to Small Brewsky Pub at 829m within 90 seconds. Allow roughly 29 seconds
for an unobstructed dry-weather drive; weather, traffic and driving may increase
actual time. Travel time is not a frame-time performance claim. Six potholes OR ten hits
wears out the auto. Four cows and 18 pedestrians include two jaywalkers.
The retained arcade roll policy requires the player's absolute pre-impact speed
strictly above 20km/h: cow contact adds a player roll impulse; vehicle contact
adds a struck-traffic roll impulse. At or below 20km/h no policy impulse is added,
but normal collision wear and penalties apply. Healthy traffic is pitch/roll
locked: idle balance, terrain and ordinary traffic bumps cannot tip it. Eligible
vehicle contact calls rollTraffic to unlock ONLY the struck body; already-crashed
or restored authorized wrecks remain unlocked and physically solid. The PLAYER
is not roll-locked: ordinary contacts, terrain or loss of balance can still cause
natural player tumbles at other speeds. Relative and post-impact speed do not
set the policy threshold. Recovery restores a safe gap without repairing wear.

PHYSICS AND LOADING
Production missions use cannon-es 0.20.0 as the authoritative rigid-body engine,
not the legacy manual/SAT player integrator: a 450kg chassis, three raycast
spring/damper wheels, gravity, friction and load-limited acceleration/coasting.
Rain reduces tyre grip from 1.15 dry to 0.8 wet. Explicit grounded braking applies
one bounded horizontal speed decrement AFTER each solved substep and contact
report, with no stacked longitudinal tyre braking or instant travel reset.
S against forward travel and W against reverse use 16m/s² dry / 14.4m/s² wet;
Space uses 20m/s² dry / 18m/s² wet. Fresh
terrain rays must confirm at least two loaded or compressed-spring wheel contacts,
chassis up >0.65 and no overturn/pending roll/recent meaningful impact. A fresh
meaningful contact preserves its impulse and suppresses braking assistance for 0.35s.
Vertical velocity, gravity, angular motion and pose are not reset; tiny suspension
settling can remain. Direction is selected once per 60Hz physics tick: S brakes
to rest before held S reverses on the next tick; W brakes reverse before forward
drive on the next tick after rest. S wins over W; Space overrides both and holds.
Reverse requests 3.5m/s², with a soft 4.5m/s force/governor target; tyre braking
opposes overspeed, not a hard speed clamp. Neutral horizontal speed below
0.001m/s uses guarded idle hold, not cancellation of real backward coasting.
Traffic uses its separate controller below. The ground-only legacy adapter
mirrors smooth braking, stop-then-opposite-drive and Space hold, with zero-time
calls inert.
The shared player steering module owns the 0.73rad lock and
1.84m wheelbase for Cannon and the legacy adapter; cockpit normalization uses
the same lock. Progressive keyboard intent responds immediately with 90ms input /
70ms neutral exponential time constants and speed-dependent wheel slew:
max(0.7, 5 / (1 + 0.18 x |speed|)) rad/s, speed in m/s. Release/countersteer
discards queued intent so key-up cannot initially increase wheel angle.
Intent is filtered BEFORE the speed-sensitive wheel limit. Live Cannon lateral
authority uses max(6.75, min(speed² / 2.07, 42)) m/s² through 32km/h, smoothly
fading extra assistance over 32-50km/h back to 6.75m/s². These are targets, not
hard bounds on solved motion. Cannon reverse lock remains 0.28rad; the legacy
adapter does not implement the live urban-force assist. Traffic's separate
0.18s target / 0.10s effort smoothing remains unchanged.

The current arcade PLAYER assist supports tight 20-30km/h half-road turns:
- Requires at least two loaded terrain-contacting wheels, chassis up-vector
  world-up component >0.65, no overturned state and no pending player policy roll.
  Meaningful collision impulses >9N·s suppress assist for 0.35s; sustained
  meaningful contacts refresh the window. Applied player policy roll impulses
  also suppress it for 0.35s. No airborne, insufficient-support, overly tipped
  or overturned assistance.
- Local desired yaw rate is 0.96 x speed x tan(steering) / wheelbase, bounded
  up to +/-4.5rad/s in the urban band, fading to +/-1.6rad/s at high speed.
  Neutral demand targets zero rate, not the original heading. Yaw error / 0.025s
  has a speed-specific acceleration bound up to +/-12rad/s², returning to
  +/-5rad/s² at rest/reverse/high speed, scaled by inertia into torque.
  These bound the controller, not collision-induced rotation.
- Lean is separate from wheel lock: its 0.28rad base limit tightens to 0.08rad
  under full urban turn load. Spring/damper gains rise from 22/7 to 80/16 and
  the roll-acceleration command bound from 8 to 24rad/s². This is bounded support,
  not a player upright lock or free uprighting; old constant-lean claims are
  superseded, not globally replaced with the steering-lock value.
- A centre-of-mass force tracks centripetal demand and 100ms slip correction
  with a speed/turn-load-specific bound above the base 4 x grip / 1.15 m/s².
  Rear-axle turning kinematics retain required COM side velocity instead of
  forcing it to zero. A compensating longitudinal force makes the tight-turn
  component energy-neutral, not a free motor or speed governor. Steering assist
  writes forces/torques and wheel steer, not body pose, velocity or angular velocity.

CURRENT USER REVISION: 20-30km/h HALF-ROAD TURN EVIDENCE
Latest 0.73rad lock / speed²/2.07 urban tuning replaces 0.70rad / speed²/2.25,
reducing measured effective radius by 6.4-6.8%. The 1.84m wheelbase, arcade
controls, 32-50km/h fade, 6.75m/s² highway cap, reverse and brakes are unchanged.
Reported centre-entry 180-degree measurements, NOT rerun for this docs edit:

HUD speed   Effective radius dry / wet   Full swept width dry / wet
20km/h      2.260 / 2.259m                6.390 / 6.362m
25km/h      2.270 / 2.274m                6.436 / 6.411m
30km/h      2.292 / 2.297m                6.524 / 6.501m

Start x=-6.3m for a +X turn / +6.3m for a -X turn, z=40m, about 1.2m INWARD
FROM THE CURB, with NEUTRAL WHEELS, not pre-lock at rest. Only entry speed is
seeded; full steering and binary W/COAST then maintain each 20/25/30km/h target.
There is NO AUTOMATIC 20-30km/h GOVERNOR or per-step velocity assignment. Radius
is effective horizontal COM distance per accumulated yaw after 0.5rad, not a
fixed full-circle radius. HUD speed is local forward speed; horizontal travel
speed is also checked to reject a hidden slowdown to walking speed.

All 36 cases cover three speeds, dry/wet, both signs and -10/0/+10cm entry offsets.
Every 240Hz substep, including the neutral-wheel entry transient, checks the
complete rendered exterior (both drivers), compound chassis shapes and full
solved tyre/rim envelopes. A 10cm guard PER EDGE exceeds measured maximum point
travel (~8.1cm). Largest reported centred width including BOTH guards is
~6.724m < 7.5m. Offset cases must fit the SAME HALF-ROAD [-7.5, 0], mirrored for
the other sign, not merely the full 15m asphalt. Tests require all three wheels
loaded on terrain, no contacts/overturn and up-vector >0.995. Turns sustain roughly
the target speed, not walking pace, in all 36 passing cases. Source regressions:
vehicle-steering-turn and vehicle-steering-test-helpers.

This approaches 3g at 30km/h: deliberately ARCADE, NOT REALISTIC vehicle handling,
an any-lane/any-speed or traffic-clearance guarantee, or FULL 360-DEGREE ROAD-FIT
PROOF. Historical pre-urban neutral-entry baseline: ~6.60-6.62m radius / ~14.87-14.93m swept
width at 20km/h; at 30km/h it hit the boundary BEFORE 180 degrees, so no successful
30km/h baseline radius is claimed.

Reported S-braking results from the existing fixed-60Hz physics fixture:
Starting speed   Weather   Time to rest   Distance to rest
30km/h           Dry       0.533s         2.176m
60km/h           Dry       1.05s          8.624m
60km/h           Wet       1.15s          9.568m
90km/h           Dry       1.55s          19.279m
These are stopping times/distances, NOT FPS or render-time measurements,
universal crash guarantees or new runs for this documentation edit.

Historical sequence (same-day passes are explicitly ordered):
1. 2026-09-11: earlier wider-steering pass reduced lock to 0.50rad; the subsequent
  keyboard-assist pass retained it, measuring about 3.56m radius.
2. 2026-09-13, first brake pass: instant horizontal stop, then held opposite
  drive on the next tick.
3. 2026-09-13, subsequent brake-only pass: S stopped/held and powered reverse
  was removed. Traffic local-heading/inertia/contact-bias fixes from that period
  remain intact; they are not reverted.
4. 2026-09-13, wider-turn/brake pass - STEERING NOW SUPERSEDED: 0.28rad lock /
  about 6.60m radius (roughly 85% wider than the earlier 3.56m),
  smooth bounded brakes, restored held-S reverse and stronger Space hold.
  This supersedes instant-stop and no-reverse/brake-only control descriptions.
  The separate static accident car does not change the fleet or traffic RNG.
5. 2026-09-13, FINE ADJUSTMENT - SUPERSEDED: 0.2785rad lock, 6.6375m dry / 6.6387m
  wet walking-speed radius; former full-road 180-degree evidence and candidate
  limits are historical, not current constraints.
  The 16/20m/s² dry brakes, wet scaling and 4.5m/s reverse target remain UNCHANGED,
  independently of steering. The wreck still faces the approach
  at x=7.05m, yaw pi, terrain-fitted on the curb and obstructing the auto corridor.
6. PREVIOUS URBAN REVISION - SUPERSEDED TUNING: 0.70rad / speed²/2.25, measured
  radii 2.426/2.424m at 20km/h, 2.448/2.454m at 30km/h (dry/wet), maximum guarded
  width 7.015m. It introduced speed-specific urban assistance, speed-dependent
  slew and release/countersteer anti-windup; 20-30km/h half-road turns replaced
  the walking-speed requirement.
7. LATEST TIGHTER-TURN REVISION: 0.73rad / speed²/2.07, with updated evidence
  above; the same arcade controls and safety limitations remain.

HISTORICAL KEYBOARD-ASSIST COMPARISON - 2026-09-11
That pass retained 0.50rad lock: geometric radius was 3.37m; measured low-speed
radius was 3.563m dry / 3.560m wet, versus about 3.533m before assistance. These
are historical, not the current tight-turn figures. Acceleration and the
then-existing brakes were unchanged. This predates the 2026-09-13 brake-only/
traffic fixes, smooth braking/restored reverse, static car and the current
tight-turn/actor/patrol revision. Its controlled 0.5s W+A response
measurements were world-X displacement from the start, in metres:

Initial speed   Before dry / wet    After dry / wet (2026-09-11)
30km/h          0.314 / 0.320        0.775 / 0.779
50km/h          0.271 / 0.279        0.703 / 0.748
90km/h          0.235 / 0.249        0.660 / 0.682

In that historical fixture, after releasing A with W held, yaw rate decayed about 95% within
200ms and local lateral drift is <0.043m/s at that sample. The auto retains its
new heading; continued world-X travel along it is not residual local sideslip.
Countersteer to point back along the road. These measurements supersede the
previous wider-pass 100ms-tap figures; they are not universal trajectories or new
benchmark runs for this documentation update.
That historical six-case dry/wet 30/50/90km/h slalom used four alternating
solid obstacles, 250ms keyboard decisions and 60m spacing (also at 90km/h).
Those runs cleared them upright, road-contained and without contacts; the
current urban controller uses shorter corrections, described under VALIDATION.
This is controlled automated evidence, NOT human playtesting, an FPS improvement,
a guarantee of easy driving or a promise that every crash can be avoided.
Real sampled terrain, solid collision profiles, upright healthy-traffic rules,
damage and automatic landing/manual recovery remain intact. Natural player
tipping and crash consequences remain possible; the player is not roll-locked.

Each 60Hz tick has four 240Hz
base intervals. Ordinary driving keeps four solves; high-speed nearby impacts,
rapid near-ground rotation and fast downward landings adaptively subdivide them,
capped at 32 solves per base interval / 128 per tick. This reduces pass-through
in tested thin-barrier, head-on and falling-canopy cases, but is bounded discrete
anti-tunnelling, NOT guaranteed continuous collision detection at arbitrary speeds.
Seven recycled 16m heightfield tiles sample curbs and the actual authored pothole
outline/depth on a 0.25m grid, not every rendered triangle.

Model-specific compound collision profiles cover the player and ALL traffic kinds,
replacing the old universal 1.1m traffic hull. The actual auto canopy reaches 1.95m
(not the old 1.86m proxy), the bus roof 3.25m and truck cargo 3.615m above model road
origin. Cars/taxis have distinct body/cabin/roof/sign parts. The tanker has a
24-sided elliptical convex tank, not a tank-sized box. Autos preserve their open
sides and three-wheel layout. Wheel cylinders and crown/sidewall/rim supports,
scooter/motorcycle riders (including triple riders), and e-bike riders and bags
have profile parts. These are rigid envelopes, not per-triangle render colliders
or separately articulated rider bodies.

Ground contact is not eight points for a whole vehicle: eight inset tangent
corner spheres per box part, plus wheel/other supports, are the cheap upright
and genuinely flat-patch path. Tipped parts near uneven ground use continuous
rounded convex panel surfaces (and a continuous elliptical tank surface), with
the corresponding corner pads disabled. This prevents the tested corner-only
gap where bus roof ends in potholes let the intact middle penetrate the road.
Contact uses the actual sampled heightfield, never a fake flat plane. The flat
shortcut checks every grid sample beneath the swept AABB, including an apron
and adjacent tiles; wide real depressions still let inverted roofs descend.

Collision materials distinguish low-resistance upright traffic wheel pads from
sliding rubber sidewalls/crowns and metal roofs. Hard-surface contact-equation
stiffness is 1e9; pairs involving organic material use 1e7. This is solver
compliance, not soft-body physics. Blockers provide collisionHeight,
collisionBaseY and collisionSurface. The base is an absolute world-space bottom,
so curb height is not added twice. Footpath metadata keeps horizontal footprints
clipped at Y <= 2m but separately retains full tagged ground-connected solid
height, such as posts and trunks. Detached overhead geometry and full foliage
envelopes do not inflate those boxes. Explicit tags select metal, concrete, wood,
rubber or organic surfaces; bus-stop glass uses the hard concrete class.

Nearby traffic uses a bounded dynamic-body pool (up to 32, roughly 42m range)
with AI force/torque control. Distant uncrashed traffic stays planner-driven.
Healthy bodies of EVERY supported traffic type use angularFactor(0, 1, 0),
cancelling only X/Z angular momentum. Idle balance, curbs, potholes, loss of
support and passive traffic-to-traffic bumps cannot authorize pitch/roll.
Translation, yaw, gravity and normal solid contacts remain physical: traffic can
be displaced or stopped, not ghost through obstacles. This is not a height lock
or per-frame pose snap.

Traffic now drives along its solved LOCAL HEADING, not independent world-X/Z
velocity targets. Lane error supplies bounded heading correction; stopped
traffic neither slides sideways toward a pending lane nor steers in place.
Local forward speed and shortest-path yaw retain 0.18s target / 0.10s effort
smoothing, initialized from actual spawn/restored motion and restarted from
solved motion during the player-contact cooldown. Drive acceleration is bounded
to +/-2m/s², or +/-7m/s² when braking; yaw acceleration to +/-3rad/s².
Intended turns add signed speed/radius yaw-rate feed-forward. Local side grip
includes speed x actual yaw rate centripetal demand and 0.04s slip correction,
bounded to +/-6m/s². These are forces/torques, not controller pose/velocity writes
or a world-Z clamp; U-turns and wrong-way motion remain valid.

Healthy traffic also zeros invInertia.x / invInertia.z and updates world inertia
so solver effective mass matches the locked axes; authorized unlocking restores
mass properties. Only NON-ORGANIC, NON-PLAYER, NON-GROUND contacts involving healthy traffic
have positional penetration-bias recovery speed bounded to 2m/s. This does NOT
reduce relative-closing-velocity impulses, friction, force limits or solid
contacts, and does not clamp body speed. Player, ground and authorized-wreck
contacts are excluded from this bias cap. ORGANIC pairs instead use the newer
finite one-way policy below, including player/wreck contacts; the old blanket
claim of unchanged organic friction/closing/bias is SUPERSEDED.

The existing strictly >20km/h pre-impact player-speed policy calls rollTraffic
to unlock only the struck body to angularFactor(1, 1, 1) before its roll impulse.
Already-crashed sources and restored authorized wrecks also remain unlocked,
with motors disabled. Compound solid shapes and physical ground support stay
active, retaining tested roof/side anti-sinking support, not animation-only wrecks.

Departed authorized wrecks leave active physics and freeze at their solved pose,
even if airborne, in a separate 32-entry pose cache. Returning unchanged sources
restore COM height, orientation, linear/angular velocity and roll authorization
without snapping upright or onto ground, even before the source's crashed flag
is set. Healthy re-entry retains height, velocity and yaw but discards unauthorized
tilt/XZ spin; natural traffic wrecks are no longer expected. Recycling, relocation
and source replacement invalidate stale poses. Player recovery preserves valid
traffic-cache entries; mission replacement/disposal clears them.
This is bounded offscreen physics LOD, not an entirely dynamic world of wrecks.
Up to 160 local blocker proxies and twelve solver iterations bound the local solve.
Organic road actors remain navigation-owned kinematic proxies, not ragdolls; riders stay rigidly
attached. There are no soft bodies or independently simulated breakable glass panes.
Millimetre-scale trim and player metal dents are cosmetic, not structural
collision detail. Simplified hulls, raycast tyres and sampled terrain are not
fully realistic or an engineering-grade simulation.

Tight AABB broadphase rejection avoids irrelevant compound/terrain pairs to avoid
a regression from extra collision shapes. Scratch reuse, unchanged static/terrain
invalidation avoidance and flat corner-support paths also limit work. The earlier
collision/handling passes made no FPS or faster-frame improvement claim; their
timings describe prior versions, not current performance. Optional one-bus
measurements are not whole-fleet or rendered-frame benchmarks. The latest
controlled comparisons below are separate evidence. Heavy crashes and uneven
bus-wreck contact remain expensive, especially long roofs bridging potholes.

Regression tests inspect actual rendered model vertices, not only collider
bounds: settled side/roof cases require the lowest visible vertex above -0.035m
relative to flat road. That tolerance allows small solver/proxy/trim imperfections,
not perfect zero penetration. Rounded terrain hulls allow less than 3cm corner
envelope excess. Tests cover uneven roofs/sides, curbs, tile seams, real
depressions, fast impacts, material/base metadata and frozen-wreck restoration.
Handling/route and one-bus terrain benchmarks are opt-in and skipped by default.
The approximately 29s clear drive, 90s deadline and 78px condition card are unchanged;
the clear-route regression allows greater than 29 and less than 45 seconds.

Historical 2026-09-10 traffic stability validation covered a 25-second, 10-vehicle
idle reproduction, including both motorcycle variants: four falls before that
lock fix, zero after, with zero non-terrain contacts. This is not a before/after
measurement of the current traffic-motion fix. Regressions verify all types through
lane changes, yaw, braking, shallow potholes and real curb descent, plus gravity,
smoothing, passive traffic bumps and struck-body-only unlocking. Wreck-cache
checks preserve authorized restoration and reject unauthorized healthy tilt.
These do not promise steep-curb climbing: solid low-speed traffic may stop against
a curb. These are correctness checks, not FPS or rendered-frame benchmarks.

Rendering follows solved chassis/traffic positions, quaternions and player
wheel suspension rather than manual bank/tumble animation. Natural player
overturn detection uses 0.25s with chassis up-vector below 0.2 world-up. Landing
upright restores controls after 0.2s on at least two loaded terrain-contacting
wheels, up-vector >=0.85, vertical speed <1.2m/s and angular speed <1.5rad/s,
with no pending roll. Airborne upright poses do not qualify. Automatic recovery
does not teleport, heal, charge points, refund time or restart an ended mission.
If stuck on a side/roof, R still resets pose/momentum for 100 points without
repairs. SAT remains for AI gap planning/recovery and
the legacy test adapter, not active player collision response. The legacy
29 + 61 constants still define the 90s deadline, not measured engine travel time.
The initial static preview imports neither Three.js nor Cannon: OPEN GAME lazily
loads the application, and START MISSION creates its physics world. The menu
does not initialize a physics world; mission replacement/menu return disposes it.

ACTOR MASS, NAVIGATION AND CONTACT OWNERSHIP
Central actor-mass values in kg preserve vehicle tuning: auto/player 450,
car/taxi 1300, bus 6500, truck/tanker 8500, scooter/motorcycle/e-bike 180,
including riders/load. Organic masses: DOG 20, PEDESTRIAN 75, COW 450.
Finite mass sets contact budgets; navigation still owns their kinematic poses.
These are NOT free-body ragdolls.

Organic contact is ONE-WAY, VEHICLE-INITIATED and reduced-mass bounded. Pair budget:
vehicleMass x actorMass / (vehicleMass + actorMass) x closingSpeed x min(1, dt/0.08),
shared across the pair's equations. Only vehicle normal closing speed above
0.03m/s contributes. Actor gait, penetration correction, external-force RHS and
organic friction DO NOT drive vehicles. Wheel-support rays ignore organic proxies,
including fallen pedestrians, so actors cannot become suspension jacks; response
is restored before actual contact solving. Enabled moving-vehicle contacts,
damage/penalties and the strictly >20km/h player-speed roll policy remain.
Dogs still add no injury/wear policy. Locomotion cannot move vehicles; a vehicle
driving into an actor still receives the finite reaction. Pooled equation
overrides are restored after solving to preserve later hard-contact behavior.

Main refreshes reusable actorObstacles after world updates from player, traffic,
construction, footpath solids and the accident-car blocker; both actor systems
receive it before physics and at visual synchronization. Actor-self blockers
are excluded, rather than feeding sceneBlockers back into navigation. Dogs,
pedestrians and cows share bounded stateful detours, synchronized visual/proxy
poses, pause-frozen route state and seeded-reset path caches. Navigation uses a
120Hz clock, 100ms catch-up bound, bounded walking/yaw and a held route clock
with committed rejoin goals, not route-phase snaps. Cached visibility graphs
are bounded to 48 local obstacles / 194 nodes; ALL supplied solids validate edges.
Blocked/invalidated edges trigger replanning with 500ms retries, NOT full graphs
every frame. Enclosed/over-budget routes WAIT; embedded egress moves only the
actor at walking speed, never teleports it or a vehicle.

Actor-physics regressions cover every species against stalled player/car bodies
at 30/60/120Hz with real enabled contacts, requiring drift/speed below 3mm /
0.003m/s, not exact zero. Other checks cover moving-vehicle impulses, fallen-actor
support exclusion, later hard-wall response, swept detours, moving-edge changes,
authored rigs, recovery, reset and pause. Wrong-way-patrol tests separately check
persistent routes/identities, repeated returns and actual Cannon hulls inside
finite windows beside physical queues. They do NOT prove arbitrary external
crashes can never shove a patrol toward a signal.

HISTORICAL TRAFFIC-MOTION EVIDENCE - BEFORE ACTOR/PATROL REVISION
The earlier traffic-motion regressions exercise the actual planner plus Cannon.
Reported measurements, not rerun for this docs-only edit:
- Stopped car/truck with pending lane demand: zero lateral displacement/slip.
- Moving car/truck lane changes in both directions: 0.022-0.038m/s peak local slip.
- Intended auto U-turns of both signs: approximately 0.0162m/s peak local slip
  and 0.0933m maximum position step at 60Hz.
- Seeds 7/41/99, terrain/pool fixture: over 800m, zero player hits/potholes,
  shallow sloped potholes under traffic, repeated pool transitions and wrong-way
  travel.
- Separate authored-world fixture with real actors/dogs: the unattended player
  hits the crossing cow near 365m, recording 4-5 hits. This is NOT a perfect
  no-hit full-route result; the test preserves real actor contacts.
Other checks cover no controller pose/velocity writes between solves,
30/60/120Hz traces, pause, solver inertia/contact-bias scope, and a walking
pedestrian pressing into a stopped truck without an explosive launch.
That contact evidence is superseded by removing organic gait/penetration/friction
drive. Old route/contact counts and open-ended auto-turn measurements do not
validate current navigation or finite patrols. These are historical
motion/correctness observations, NOT an old benchmark rerun, FPS claim,
human playtest or supported numerical before/after comparison for the current
fixes. Historical steering/CPU/GPU evidence remains separate.

AUTO GRAPHICS AND HISTORICAL GPU COMPARISON - 2026-09-13
Graphics defaults to AUTO at HIGH. Button cycle: AUTO -> HIGH -> SMOOTH -> BALANCED
-> LOW. Explicit presets are manual and do not adapt.

AUTO level               DPR cap   Sun shadow map
HIGH (initial)           1.5       2048 x 2048
SMOOTH                   1.25      2048 x 2048
BALANCED (AUTO floor)    1.0       1024 x 1024

Actual DPR is capped by the device DPR too. Manual LOW remains available at a
0.8 cap with shadows off; AUTO never selects LOW. Four consecutive ~500ms windows
averaging >22ms, with at least 50% slow frames, lower one step after roughly 2s.
Twenty windows averaging <18ms, with at least 90% fast frames, raise one step
after roughly 10s. Isolated hitches do not qualify. Paused, menu, ended, hidden
and manual frames are excluded; transitions clear evidence, not the retained level.

Automatic changes affect DRAWING-BUFFER AND SHADOW-MAP RESOLUTION, not source
assets, texture resolution, geometry, render distance or physics ticks. SMOOTH
keeps HIGH lighting/shadows; AUTO trades pixel/shadow detail for lower render cost.

Historical browser comparison, before the brake-only/traffic fixes and later
smooth-braking/restored-reverse, static-car and current tight-turn/actor/patrol revisions:
same paused frame at 1160 x 649 CSS pixels, two
trials in reverse preset order, each using 30 warm-up + 120 measured renders per
preset. Actual GPU timer queries produced these means across both trials:
Preset       GPU time    Renderer CPU time
HIGH         2.82ms      2.38ms
SMOOTH       2.65ms      2.42ms
BALANCED     2.50ms      2.41ms

All presets preserved 769 draw calls / 1,063,128 triangles for that frame.
These isolated render timings are NOT real gameplay FPS, a CPU-physics
improvement or a live-route benchmark. No new measurement was run for this doc edit.

LATEST CLEANUP AND CPU EVIDENCE - 2026-09-14, FIXED QUALITY
Cleanup removes the redundant player-quote background fill and repeated patrol
work: clearance expands once per swept sample, not per obstacle. planningObstacles
conservatively filters certainly distant candidates AFRESH PER PLAN, retaining
order and all possible overlaps, with NO candidate cap or mutable-pose cache.
noUnusedLocals / noUnusedParameters were already enforced: no new unused-import/
local purge or claim that every unused line is gone. The legacy test/menu adapter
remains used. Actor behavior, finite patrol routes, wrecks and assets are unchanged.

Reported A/B swapped the EXACT ORIGINAL METHOD ORACLE and optimized updatePatrol
on the actual main simulation. Three fully warmed 900-frame route runs (old ->
new -> old), synthetic 60Hz, first 60 frames discarded each, manual HIGH,
968 x 683 drawing buffer, actual DPR 1, 2048 x 2048 shadows:
CPU metric                      Before -> after
Callback mean                   13.29 -> 10.86ms (~18.3% reduction)
Callback p95                    29.30 -> 24.30ms
Callback maximum                32.90 -> 30.80ms
Patrol mean                     2.670 -> 0.184ms (93.1% reduction)
Total planning mean             5.123 -> 2.641ms (includes patrol; not additive)
Actual GPU-query means 3.498 -> 3.554ms were essentially unchanged: NO GPU or
observed-FPS improvement claim. All three 900-tick / 3,758-substep traces matched
poses, contacts, events and per-frame draws/triangles EXACTLY. Same endpoint:
~395m, one hit, zero potholes, player rolled, 10% wear; NOT the full 829m route.
922 meshes / 677 casters, warmed GPU 534 geometries / 31 textures / 39 programs
preserved with NO QUALITY CUTS. Reported browser errors: ZERO. The 24.30ms p95
still exceeds 16.67ms: NO zero-FPS-drop guarantee, especially in costly contacts.
These supplied results were not rerun for this docs-only edit; older comparisons
below remain historical, not additive.

HISTORICAL CPU PERFORMANCE - 2026-09-11, FIXED QUALITY
This earlier pass removed impossible-pair work without reducing quality. These
measurements and inventory describe that snapshot, before current damage/AUTO,
brake-only and traffic-motion changes, and before the latest smooth braking,
restored reverse, static car and current tight-turn/actor/patrol revision;
these are not current whole-game counts:
- Physics caches AABB bounds of the actual six heightfield-pillar vertices in a
  WeakMap, refreshed on rebuild and released with discarded pillars. The
  sphereConvex guard rejects only disjoint sphere/pillar bounds, with a rounding
  apron. Possible contacts still use Cannon's exact existing resolvers, dispatch,
  shape/contact order, materials and friction. Unregistered terrain and non-unit
  transforms fall back to Cannon; terrain and colliders are not simplified.
- AI overlap checks use a yaw-independent bounding early-out before SAT axes or
  trigonometry, rejecting only unreachable pairs. Nearby checks retain exact SAT
  tie order and contact values. Finite/nonnegative guards preserve fallbacks;
  there are no cached poses or dimensions to become stale.

Controlled physics comparison: Apple M3 Max, 14-core CPU, Node 24.14.
CPU milliseconds PER SIMULATION STEP, not rendered frames:
Workload               Mean before -> after     p95 before -> after
600-step physics run   13.55 -> 10.05ms          20.53 -> 14.68ms
Full-route run         15.15 -> 10.56ms          38.33 -> 23.42ms
Repeated controlled physics traces had identical before/after state hashes.

SEPARATE moving-browser AI comparison: HIGH, 1452 x 1024 drawing buffer,
2048 x 2048 shadows. Controlled invocation of the actual frame callback measured
mean frame CPU 12.96 -> 11.60ms, including traffic AI 4.47 -> 2.98ms and renderer
CPU 2.37 -> 2.38ms; GPU time was 4.12 -> 4.01ms. Background requestAnimationFrame
was throttled: this is callback-work timing, NOT observed live FPS. Browser state
hashes and per-frame draw/triangle counts matched exactly before/after.

In that comparison, assets, faces, models and draw counts matched. Historical inventory:
914 meshes, 631 geometries, 106 materials, 33 textures, 675 shadow casters;
32 traffic vehicles, 22 road actors, 16 dogs, 539 static blockers. These are
scene/resource counts, not draw counts or FPS. No render-distance reduction,
dropped simulation ticks, frozen shadows, lower texture/shadow resolution or
removed collision detail funded those CPU savings. This is separate from AUTO's
current drawing-buffer/shadow-resolution changes. Node and browser results isolate
different work: do not add reductions or component timings into a combined
speedup. These are measured CPU savings, NOT guaranteed FPS; heavy crashes and
uneven-terrain contacts can still be costly. Source assets, URLs and licenses
are unchanged.

CURRENT FEATURES
- Crossing pedestrians have corrected, connected two-hand arm chains. Sixteen
  dogs comprise 14 sidewalk dogs and two randomized road crossers, with randomized
  coats and seeded reset behavior. They freeze on pause and act as non-injury
  solid blockers without extra damage; mission restart resets their seeded state.
- Traffic AI targets asphalt lanes; impacts can displace nearby rigid bodies.
  Only the player is intentionally routed through clear footpath gaps. Static
  street furniture, all 16 dogs and road actors block driving and safe-gap
  recovery. The sampled smooth curb supports physical wheel/chassis motion;
  curb elevation alone adds no special wear penalty.
- A/D or left/right arrows accumulate heading while moving. The current 0.73rad
  lock / 1.84m wheelbase and speed-specific assist support a clear-road 20-30km/h
  180-degree turn in ONE 7.5m road half: start about 1.2m inward from the curb,
  straight wheels, hold steering and alternate W/coast to maintain speed.
  NO automatic urban governor. Latest measured radius is 6.4-6.8% smaller than
  the previous 0.70rad / speed²/2.25 revision; see full swept-envelope evidence above.
  This approaches 3g at 30km/h:
  ARCADE, NOT REALISTIC, any-lane/any-speed or full-360-degree road-fit proof.
  The old 0.2785rad / ~6.64m walking-speed wide-circle revision is SUPERSEDED.
  Releasing steering straightens the wheel, not the body; heading persists with
  no stationary spin. The high-speed lateral target at 50km/h and above remains
  6.75m/s². The engine
  solves forces/torques without a pose/heading snap, and assistance yields to
  meaningful collisions, policy rolls, lost support and overturning.
  The forward governor target is 30m/s. Requested 11m/s² forward acceleration
  remains traction-limited, not guaranteed. Powered reverse requests 3.5m/s²
  toward a 4.5m/s governor target. Grounded player braking is smooth bounded
  arcade deceleration, not the previous instant stop or a fully traction-limited
  brake model. Impacts can briefly exceed governor targets; tyre braking opposes
  overspeed rather than a hard clamp. The 829m route
  and 90s deadline are unchanged.
- Positive local forward speed with route velocity cos(heading) x speed < -0.5m/s shows a steady,
  transparent fullscreen amber warning: "Wrong way!" (struck through), followed
  by "Not the fastest way!". Powered or external local backward motion never triggers it. It hides
  when stopped, paused or on the home screen. No forced turn or wrong-way penalty;
  normal collision consequences and the mission deadline still apply.
- Compact AUTO CONDITION card: 78px high, down from 155px, at 320px wide.
  The title, overall percentage, five-stage wear label and separate pothole/hit
  counts remain visible. The original progressive SVG auto geometry is retained
  at 60 x 32px display size. Flavor text, gauge, remaining allowances and event
  note are visually hidden but screen-reader accessible; decorative pips are
  hidden visually and from assistive technology. The card's hover title includes
  the full event note and independent six potholes OR ten hits, not combined,
  limit. Accessible announcements retain cause and actual displayed condition
  loss, including unchanged overall wear. A brief steady outline follows
  simulation time and freezes on pause; new runs clear previous feedback.
  The SVG illustrates cumulative wear, not measured subsystem damage or new
  textures. No new damage rules or repairs.
- Impact-local body damage deforms actual player-owned cloned metal vertices
  and normals, not floating dent plaques or shared traffic geometry. Selective
  metal subdivision adds 6,740 triangles. Twelve bounded impact records feed
  one pooled scuff draw with 144 triangles of capacity. Potholes add low
  wheel-arch mud/scuffs, not windshield cracks or metal crumpling. This is
  cosmetic deformation, not soft-body physics or structural/engineering realism.
- Actual exterior paint and canopy colors fade with cumulative wear; layered
  primer/bare-metal chips and raised paint edges follow the dented surface.
  Cockpit paint, padding and canopy trim also wear. Physical speed/fuel instrument
  lenses crack at 33% wear, gain branching cracks and actual missing rim wedges
  at 66%, then wider chips and extra forks at 100%. These are wear percentages,
  not remaining condition. There is NO fake windshield overlay; the road view
  remains open. Cached geometry updates colors only on changed 1%-quantized wear,
  refreshes chip positions on impacts/reset, and changes lens-stage draw ranges
  without stable per-frame buffer uploads. Main updates exterior and cockpit
  wear in every camera mode, including the final losing frame. R/camera changes
  do not repair it; new mission/menu resets clear it. No new textures, damage
  rules or simulated shards.
- Rear polish adds recessed louvers, hatch seams/hinges, canopy welts and vertical
  lamp details: two cached draws / 3,748 triangles, plus one original 64 x 128
  lens-height map. The player photograph and English/Kannada wording are unchanged.
  The visually studied rear photograph is a reference only, not a bundled image.
- Three permanent seeded bus stops. Midpoint excavation/excavator at 414m retains
  barricades. Keep screen-left through the roadworks' auto corridor at world
  x=5.55-7.45, but the wreck OBSTRUCTS this corridor farther ahead at z=440m;
  leave it for a clear lane before reaching the car.
- ONE original unbranded static wreck car on the driver's LEFT travelling +Z,
  at x=+7.05m, z=440m, yaw pi: damaged FRONT FACES THE APPROACHING CAMERA.
  Terrain-fitted y~0.06999m, body-local roll~-0.08871rad seats all four tyres
  within 0.1mm of road/curb support in the geometry regression, without scaling.
  About 23% of the actual convex footprint is on the footpath / 77% on the road,
  not a nominal-width split. The auto corridor is now OBSTRUCTED; remaining lanes
  at x=-4.5/0/+4.5m clear this wreck, not necessarily other traffic/hazards.
  The former x=+4.5m, yaw 0 regular-lane placement is SUPERSEDED. Actual mesh details:
  dented/folded hood, hanging bumper, flattened tyre, buckled rim and jagged glass
  remnants around REAL MISSING WINDOW PANES, not crack overlays or an opaque cabin
  shell. Empty seats, NO PEOPLE/occupants, and 32 decorative static road-glass
  shards. One finite placement, never a 500m repeat or traffic fleet vehicle;
  visible within 220m along Z only while the supporting road chunk is loaded.
  Three batches/main-pass draws / 4,909 triangles, unchanged with NO ASSET-QUALITY
  CUTS, NO TEXTURES or simulated debris.
  One solid metal-envelope blocker is exposed separately as world.accidentBlockers
  and joined into sceneBlockers in main for driving and safe-gap recovery.
  Its yaw-oriented envelope comes from transformed WHOLE-BODY vertices and
  absolute Y bounds, excluding only decorative road shards, not the obsolete
  unrotated placement. It encloses the tilted car, not triangle-exact collision.
  The original 539 footpath blocker footprints remain unchanged. No traffic RNG,
  traffic templates or fleet changes; all previous traffic-motion fixes remain.
  Original project-authored geometry, no imported asset or new license; existing
  reference URLs, rights caveats and complete notices are unchanged.
- Seven centre-lane flyover piers at x=0 and z=466/494/522/550/578/606/634m. Individual
  base colliders leave open gaps; no continuous deck or flyover barricades.
  At 466m the first pier is a 2.6m stub with no overhang; at 494m it is a partial
  5.2m build; the remaining five reach 8.2m.
- Recurring normal/sunny/cloudy/rainy weather: seeded 8-12/8-12/6-10/5-6 seconds.
  Two 18-26m waterlogged stretches begin at 340m and 660m and persist after first
  rain until reset. Rain/water are procedural. Simulation-time weather pauses.
- Dynamic temperature HUD: fictional ambient 24-31 degrees Celsius, dependent on
  simulated weather, not live weather data.
- 32 fleet slots: the original 28 plus four wrong-way delivery e-bikes, IDs 28-31
  (Tomato, Krypto, ThinkIt, Tomato respectively). All ten delivery e-bike bodies
  are fixed light blue #70c9e2, including wrong-way bikes, regardless of traffic
  paint. Unbranded electric twist-throttle step-through mopeds have integrated
  batteries, rear hub motors, black seats, brake levers/cables and both feet on
  fixed footrests: no pedals, chains or pedalling, and no Yulu logo.
  Bag colors/labels are unchanged: four Tomato, three Krypto and three yellow/green
  ThinkIt bags; two tankers remain.
  Four Sober cars/taxis carry ordinary lettering on both sides and the rear.
- Wrong-way delivery IDs 28-31 and existing autos 4/18 follow finite oval patrols:
  4/28 at z=130-215m, 18/29 at 330-385m, 30/31 at 730-815m. Target 1.9m/s (~7km/h),
  1.65m-radius caps; both return turns are PREPLANNED, not the former open-ended
  2.2m auto-turn followed by unrestricted wrong-way travel. Driver-RIGHT when
  travelling +Z is NEGATIVE X, not left. AI-only reservations leave MIDDLE/LEFT
  for regular traffic and are not invisible colliders. Windows avoid junction
  queues; tested hulls stay more than 35m from signals. All six preserve route
  and identity through pooling/revisits: no active-body teleport or route-end
  respawn. Actual crashes remain WRECKS, not automatically healed patrols.
  No guarantee prevents an external impact from shoving one toward a signal.
  Fleet count remains 32; the separate x=7.05m, yaw pi static wreck is unchanged.
- Both triple-rider motorcycles remain alongside all ten delivery e-bikes.
  Two motorcycles, IDs 13 and 27, each carry three adult young men with randomized
  starts. Riders on both triple motorcycles and all ten delivery e-bikes have
  natural hair and no helmets.
- Enhanced 3D vehicles, people and facades, with pavement and drains.
  Procedural surface grain, road wear and mud texture use no external surface images.
- Selective inward bevels refine visible vehicle hardware, architecture and actor
  details without inflating authored silhouettes, outer bounds or collision
  footprints. Wheels, tubes and organic people/cow/dog curves are selectively
  smoother. Broad faces keep flat normals rather than pinched shading; curves
  keep analytic or correctly transformed normals, not whole-batch smoothing.
  Geometry checks cover finite unit normals and nondegenerate refined triangles;
  collapsed vehicle rounded-box strips are removed with authored normals retained.
- Physically based actor surfaces use three shared generated 1024 x 512 atlases:
  sRGB tint, linear packed height/roughness/metalness, and linear clearcoat-mask/
  clearcoat-roughness data. Opaque vehicle batches use MeshPhysicalMaterial with
  clearcoat on paint ONLY. Bare metal, rubber/tyre sidewalls, cloth, leather, skin
  and hair are not clearcoated, including riders sharing a vehicle batch. Only
  exposed metal is metallic. Standalone road actors and player drivers remain
  MeshStandardMaterial, using tint and physical maps without clearcoat.
  Multiscale grain, directional metal machining, rubber pores, woven cloth,
  leather creases and fine skin pores refine surfaces. Five explicit tile-local
  mip levels use linear-light tint downsampling and edge-extruded gutters to
  keep material classes separate.
- World surfaces use four cached 512 x 512 maps per world: color and packed
  height/roughness pairs for the plaster/concrete/paving atlas and asphalt.
  Coarse angular aggregate and subtle tar-repair patches refine asphalt;
  concrete gains pores, spalling and rain-streak stains. Manual mip chains
  area-average color in linear light and keep packed data linear. Wrapped gutters
  and per-level atlas sampling prevent neighboring material bleed. Vehicle and
  world glass use nonmetallic MeshPhysicalMaterial with IOR 1.5, no transmission.
  These material refinements retain existing batches without extra geometry or
  draw calls. Bump detail complements geometry normals. No external models,
  scans, surface images or normal maps were imported. This is more realistic
  procedural rendering, not photorealism; these surface-only refinements do not
  themselves alter physics or gameplay.
- Asphalt uses world-space XZ sampling with smooth warping and correlated
  color/height/roughness variation to break up obvious tile repeats. It retains
  the 4m tile, existing maps and five texture reads; extra shader arithmetic adds
  no textures, geometry or draw calls.
- Thirty seeded tree crowns vary height, aspect, lean, orientation, branches and
  leaf-card silhouettes, with varied alpha-tested shadows from the same baked
  geometry. Crowns stay stable across frames, resets and chunk recycling; the
  existing 500m pool still repeats. Trunks, tree-island blockers and street layout
  are unchanged. Variation adds no textures, materials, draw calls, vertices or
  per-frame resources, and imports no foliage images.
- Original outdoor sky/ground gradient and broad sun replace the indoor room
  environment. Three.js PMREM filters it once per lighting instance; the neutral
  environment texture is reused, not regenerated each frame or weather change.
  Simulation-time weather adjusts environment intensity, visible sky, sun,
  hemisphere fill and fog. Physically based daylight uses stabilized PCF shadows:
  HIGH/SMOOTH 2048 x 2048, BALANCED 1024 x 1024, LOW off. Output is ACES filmic tone
  mapping at exposure 0.95 with sRGB. No downloaded HDR environment is used.
- C cycles LOW CHASE -> ELEVATED -> COCKPIT. New runs and returning to the menu
  reset the driving-camera selection to LOW CHASE. This restores the original
  settled eye 4.9m above ground, 9.5m behind the auto, targeting 1.05m above ground
  and 11m ahead (about 10.64-degree elevation). ELEVATED is a 45-degree view with
  10m look-ahead. Both exterior views now follow the auto's heading using
  shortest-angle interpolation across +/-pi, staying upright through rollover.
  Home preview is unchanged:
  45-degree-azimuth front-quarter view at 18-degree elevation, keeping the driver
  visible beside the title. Exterior transitions are smooth; entering/leaving
  cockpit snaps rather than passing through the body. Camera motion pauses.
- COCKPIT is original physical 3D geometry, not a screen overlay: two hands wrap
  the turning handlebar, with Anna's khaki or Akka's teal sleeves and rolled cuffs.
  Detailed PBR controls include ribbed grips, levers/cables, gauges, a speed-driven
  needle, switches and key. All side-panel-* geometry and hardware are removed,
  including liners, padded rails, quarter panels, sills, ribs, fasteners and grab
  handles. The roof/canopy and cloth liner remain. The wider front opening matches
  the exterior body frame and widened windshield glass within the unchanged auto
  footprint. The former 17,380-triangle/four-draw cockpit now also includes
  physical instrument lenses and cached wear detail; totals vary with wear,
  with no extra textures.
  An open cockpit windshield frame with no glass plane keeps
  the actual road visible. The eye follows the actual auto yaw, suspension and
  tumble. Exterior color/depth masking clears the view but preserves its shadow.
  Cached geometry and both sleeve variants reuse existing actor surface maps;
  no new cockpit texture or external image is introduced.
- 5M Mall of Koramangala, Yantri Square, Mall of World and TVR cinema.
  TVR has five original CGF - Colar Gold Fields posters, with the full title
  painted COLAR GOLD FIELDS (Colar, not Kolar). The existing Bollywood-inspired
  fictional faces and composition remain, not external poster images or real-film
  reproductions. A 17.5m pylon, front-lit TVR CINEMAS sign, marquee and ticket counter
  complete the frontage.
  Shop lettering includes exactly Mysore Silk House.
  Two seeded Shirtaloons; TONY at 73m before the 85m signal; Cafe Coffee Night at
  125m; vegetarian shops every 100m from 9m through 809m; four Hidden gem in
  Bengaluru courtyards at 158/358/558/658m, exactly TWO LEFT / TWO RIGHT along the
  forward +Z route (+X is left). Seeded placement prefers left at 158/558m and
  falls back to 358/658m as needed to preserve seeded Shirtaloons and all non-gem
  placements. Existing courtyard geometry, clear entrances and readable signs
  remain on both sides; no extra repeated shops. Finite storefronts do not recycle.
- Ganesh Juice Point at x=-14.7m, z=25m has an orange/green frontage with fruit
  crates and serving cups. Agarwal Medicals at x=+14m, z=41m has a green/white
  frontage, GREEN cross and stocked medicine shelves. Both replace real
  opening-block shops once along the finite route, not every 500m. Existing
  features and seeded placements, including Mysore Silk House, remain intact.
  Original procedural signs occupy 63 of 64 existing atlas slots. The two shops
  add eight geometry batches / 3,452 triangles, reusing existing materials and
  textures: no new materials/textures or per-frame/reset resource growth.
- All four traffic autos use seeded shuffled local images, stable through frames
  and recycling and reshuffled on a new mission without rebuilding models. Three
  of seven requested sources (2/3/4) were retrieved; 1/5/6/7 returned HTTP 403
  Forbidden, with no bypass or substitution. All three appear before a necessary
  repeat; four unique selections are supported if seven become available.
  Menu uses the default seed. Full images fit within 0.48 x 0.49m without stretch,
  crop or extra draws. Local textures/materials load only after OPEN GAME, once
  per available source, three now and at most seven. No remote runtime fetching.
  The player's original photograph is unchanged and separate from traffic.
  The player's photo is centered at x=0, y=1.48m and is 0.38m wide.
  The player's actual rear plane preserves the exact quote
  'Love is like a walk in the park... Jurassic Park' in a horizontal two-line
  lettering area, 1.12 x 0.18m at x=0, y=1.15m below the photograph, not inclined
  corner text. The former cream/white canvas backing is REMOVED: only the
  background is transparent, exposing body paint; original dark-green ink stays
  at FULL OPACITY. English/Kannada wording, portrait, front names, louvers, trim
  and progressive wear detail remain; traffic artwork is separate and untouched.
- All five autos, including the player and four traffic autos, have front names
  from the exact ten-name list: Appu, Nimma Preethi, Auto Raja, King, Yella Maaye,
  Bengaluru Huduga, Ramu, Lover, Auto King, DON. A seeded shuffle assigns the player
  first then traffic autos, without repeats among the first five. New missions
  reshuffle; frames and recycling preserve assignments. One shared 512 x 640
  atlas, one material and ten shared two-triangle badge geometries load lazily
  after OPEN GAME. Each auto adds one draw call; reassignments reuse resources
  without per-frame cache growth. Player rear photograph and quote are preserved.
- Original synthesized engine, horns, potholes and collision sounds, not recordings.
  Nearby horns vary in pitch, gain, pan and duration; at most four ambient voices
  overlap. Pause and mute stop audio.
- Pause freezes all simulation-driven behavior: traffic/U-turns, actors, dogs,
  weather, temperature and animation. A mission restart resets all gameplay
  systems and seeded state. R only recovers; it does not restart or repair.

VALIDATION
Steering regressions cover low-speed turns, progressive taps, reversal, centering,
pause/reset and 30/60/120Hz consistency. Dedicated turn regressions check guarded
20/25/30km/h HALF-ROAD 180-degree swept-body/tyre clearance with neutral-wheel
entry, both signs and +/-10cm offsets, REPLACING the walking-speed/candidate tests.
Arcade tests cover speed-specific dodges,
six dry/wet speed-combination solid-obstacle slaloms, timed countersteering,
release settling, sustained steering, externally induced backward motion and
collision/airborne assist gating. Current slaloms use 100ms URBAN / 250ms HIGHWAY
keyboard corrections; earlier all-250ms comparisons are historical. Steering
checks retain release/countersteer anti-windup and no body pose/velocity writes.
Tests and the production build were not rerun for this documentation-only update;
no archive was rebuilt. Historical results do not validate the current snapshot.
No human-playtest result is claimed. Additional source regressions cover direct
start/optional driver selection, smooth braking, held-S reverse, W brake-then-forward,
Space stop-hold and idle/support/crash gating, static wreck geometry/real missing
panes/approaching-camera front visibility/four-tyre support/footprint split/finite
visibility/transformed blocker integration/remaining-lane clearance and resource ownership,
two-sided seeded gems, exterior/instrument wear and AUTO quality timing.
Shop tests check finite placement, visible details
and resource reuse; existing resource-count expectations were updated for the
eight added batches, not new materials or textures. Optional steering profiling
uses VITE_PHYSICS_PROFILE=1 and remains separate from default correctness tests.

ASSETS AND RIGHTS
Geometry, signs, the preview, emblems and textures other than the supplied player
photo and traffic images are original procedural art. The player photo source is:
https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTJd2hIC0g1NRWE4JYB0ob-1JNmPHMrd3XSr5QSwJzmOXx85_zo-rPZgv8&s=10
The first photograph is restored at 345 x 352 pixels, bundled from the local
source asset src/assets/shankar-nag-portrait.jpg. Production assets may be hashed.
The intervening 443 x 451 photograph and its source are superseded.
This is a third-party photograph; distribution permission has not been verified.
The publisher is responsible for verifying rights before
redistribution; software/font licenses do not grant rights to the photograph.
The player file is unchanged: 14,719 bytes, SHA-256
972a93a80c248cabc8ae49af7d600a05d372006820163b946037468eec5bc842.
The three bundled traffic derivatives are JPEGs, at most 512px: source 2 is
202 x 250 / 9,718 bytes; source 3 is 512 x 315 / 11,337 bytes; source 4 is
512 x 342 / 21,566 bytes. Total 42,621 bytes. Complete seven-source URLs,
HTTP 403 failures and SHA-256 hashes are in THIRD-PARTY-NOTICES.txt.
Traffic image rights are NOT VERIFIED. No blanket CC/free-use claim is made;
site names and stock-photo article titles do not establish image-specific rights.
Verify copyright and any likeness/other permissions before redistribution.
The user also supplied this stock cockpit reference URL ONLY:
https://as1.ftcdn.net/jpg/01/92/46/30/1000_F_192463009_XqQaABtyRdps3ybYpR9y4iNNobuE7DoU.jpg
It is not downloaded or bundled, and is not requested during play. Only the
general open-front/narrow-pillar idea informs the original procedural cockpit,
not copied pixels, logos or dashboard artwork. Providing the URL grants no
redistribution rights to the stock image. This reference is separate from the
bundled portrait and its unverified distribution permission.
Rear construction reference, visually studied but not bundled or fetched in play:
https://commons.wikimedia.org/wiki/File:2023_Bajaj_RE_45_Compact.jpg
The green/yellow procedural interpretation uses general construction details,
not copied pixels or a branded replica. This reference is separate from the
unchanged bundled player portrait and its unverified redistribution rights.
No unused starter icons are included. User-supplied names are
fictional labels, not verified businesses; no copied brand logos are used.
No affiliation, sponsorship or endorsement is claimed. This is not a guarantee
of legal clearance; publishers should review names, presentation and additions.
THIRD-PARTY-NOTICES.txt contains the complete Three.js MIT, cannon-es 0.20.0 MIT
(Copyright (c) 2015 cannon.js Authors) and Noto Sans Kannada SIL OFL 1.1 notices.
Retain all complete notices with redistributed builds. Fonts are local/system
fonts, with no remote font requests.

CODE BY CHATGPT ASTRA
CONCEPT & CREATIVE DIRECTION BY AMALKRISHNA P S
In-game credits are uppercase and left-aligned in the same style as passenger text.

The illustrated preview needs neither JavaScript nor WebGL, but hosting-service
capture/upload success still needs verification. Build and repackage source changes
before upload. Replace old shared archives separately; cleaning this project cannot
recall previously published files.