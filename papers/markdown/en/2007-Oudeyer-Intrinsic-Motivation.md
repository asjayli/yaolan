# 2007-Oudeyer-Intrinsic-Motivation

> **来源**：../2007-Oudeyer-Intrinsic-Motivation.pdf ｜ **转换**：markitdown（文本提取，标题层级待恢复） ｜ 英文原文 markdown

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007 265
Intrinsic Motivation Systems for Autonomous
Mental Development
Pierre-YvesOudeyer, FrédéricKaplan, and VerenaV. Hafner
Abstract—Exploratory activities seem to be intrinsically re- A. DevelopmentisProgressiveandIncremental
wardingforchildrenandcrucialfortheircognitivedevelopment.
Firstofall,developmentinvolvestheprogressiveincreaseof
Can a machine be endowed with such an intrinsic motivation
system?Thisisthequestionwestudyinthispaper,presentinga thecomplexityoftheactivitiesofchildrenwithanassociatedin-
number of computational systems that try to capture this drive creaseoftheircapabilities.Moreover,infants’activitiesalways
towards novel or curious situations. After discussing related haveacomplexitywhichiswell-fittedtotheircurrentcapabili-
research coming from developmental psychology, neuroscience, ties.Childrenundergoadevelopmentalsequenceduringwhich
developmental robotics, and active learning, this paper presents
eachnew skillis acquiredonly whenassociated cognitiveand
the mechanism of Intelligent Adaptive Curiosity, an intrinsic
morphologicalstructuresareready.Forexample,childrenfirst
motivation system which pushes a robot towards situations in
which it maximizes its learning progress. This drive makes the learn to roll over, then to crawl and sit, and only when these
robotfocusonsituationswhichareneithertoopredictablenortoo skillsareoperational,theybegintolearnhowtostand.Devel-
unpredictable,thuspermittingautonomousmentaldevelopment. opmentisprogressiveandincremental.Takinginspirationfrom
The complexity of the robot’s activities autonomously increases theseobservations,someroboticistsarguethatlearningagiven
and complex developmental sequences self-organize without
taskcouldbemademucheasierforarobotifitfollowedade-
being constructed in a supervised manner. Two experiments are
velopmentalsequence(e.g.,“learningformeasymission”[3]).
presented illustrating the stage-like organization emerging with
this mechanism. In one of them, a physical robot is placed on a However,veryoften,thedevelopmentalsequenceiscraftedby
babyplaymatwithobjectsthatitcanlearntomanipulate.Exper- hand:roboticistsmanuallybuildsimplerversionsofacomplex
imentalresultsshowthattherobotfirstspendstimeinsituations taskandputtherobotsuccessivelyinversionsofthetaskofin-
which are easy to learn, then shifts its attention progressively to
creasingcomplexity.Forexample,iftheywanttoteacharobot
situations of increasing difficulty, avoiding situations in which
the grammar of a language, theyfirst give it examplesof very
nothingcanbelearned.Finally,thesevariousresultsarediscussed
inrelationtomorecomplexformsofbehavioralorganizationand simple sentences with few words, and progressively they add
datacomingfromdevelopmentalpsychology. newtypesofgrammaticalconstructionsandcomplicationssuch
as nested subordinates [4]. This technique is useful in many
IndexTerms—Activelearning,autonomy,behavior,complexity,
curiosity, development, developmental trajectory, epigenetic cases, but has shortcomings which limit our capacity to build
robotics, intrinsic motivation, learning, reinforcement learning, robotsthatdevelopinanopen-endedmanner.Indeed,thisisnot
values. practical.Foreachtaskthatonewantstherobottolearn,onehas
todesignversionsofthistaskofincreasingcomplexity,andone
alsohastodesignmanuallyarewardfunctiondedicatedtothis
I. THECHALLENGEOFAUTONOMOUSMENTALDEVELOPMENT
particulartask.Thismightbeallrightifoneisinterestedinonly
ALLhumansdevelopinanautonomousopen-endedmanner
oneortwotasks,butarobotcapableoflifelonglearningshould
through lifelong learning. So far, no robot has this ca- eventuallybeabletoperformthousandsoftasks,andevenifone
pacity. Building such a robot is one of the greatest challenges would engage in such a daunting task of manually designing
toroboticstoday,andisthelong-termgoalofthegrowingfield thousands of specific reward functions, there is another limit.
of developmental robotics [1], [2]. This paper explores a pos- Therobotisequippedwithalearningmachinewhoselearning
sibleroutetowardssuchagoal.Ourapproachisinspiredbyde- biasesareoftennotintuitive:thismeansthatitisalsoconcep-
velopmental psychology and our ambition is to build systems tuallydifficultmostofthetimetothinkofsimplerversionsof
featuringsomeofthefundamentalaspectsofaninfant’sdevel- ataskthatmighthelpthe robot.Itisoftenthe casethata task
opment. More precisely, two remarkable properties of human thatoneconsiderstobeeasierforarobotmightturnoutinfact
infantdevelopmentinspireus. tobemoredifficult.
B. DevelopmentisAutonomousandActive
ManuscriptreceivedMarch30,2005;revisedOctober3,2005.Thiswork
wassupportedinpartbytheECAGENTSprojectfoundedbytheFutureand Thisleadsustoasecondpropertyofchilddevelopmentfrom
EmergingTechnologiesProgram(IST-FET)oftheEuropeanCommunityunder
which we should be inspired: it is autonomous and active. Of
EUR&DContractIST-2003-1940.
P.-Y.OudeyeriswiththeSonyComputerScienceLaboratory,Paris6,rue course, adults help by scaffolding children’s environment, but
Amyot75005,Paris,France(e-mail:oudeyer@csl.sony.fr). thisis just a help. Eventually,theydecide by themselveswhat
F. Kaplan is with CRAFT-Ecole Polytechnique Fédérale de Lausanne
theydo,whattheyareinterestedin,andwhattheirlearningsit-
(EPFL),LausanneCH-1015,Switzerland(e-mail:frederic.kaplan@epfl.ch).
V.V.HafneriswiththeSonyComputerScienceLaboratoryParis,Amyot uationsare.Theyarenotforcedtolearnthetaskssuggestedby
75005, Paris, France (e-mail: hafner@csl.sony.fr) and also with DAI Labor, adults, and they can invent their own. Thus, they construct by
10587Berlin,Germany(e-mail:vvh@ieee.org). themselvestheirdevelopmentalsequence.Anyonewhohasever
Colorversionsofoneormoreofthefiguresinthispaperareavailableonline
played with an infant in its first year knows,for example,that
athttp://ieeexplore.ieee.org.
DigitalObjectIdentifier10.1109/TEVC.2006.890271 itisextremelydifficulttogetthechildtoplaywithatoythatis
1089-778X/$25.00©2007IEEE

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
266 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
chosenbytheadultifothertoysandobjectsarearound.Infact, were those with an intermediate level of novelty, between al-
mostoftenthetoysthatwethinkareadaptedtothemandwill ready familiar and completely new situations. This theory has
pleasethemarenotthe onestheyprefer.Theycanhavemuch strong resonance points with the theory of flow developed by
more fun and instructive play experiences with adult objects, Csikszentmihalyi [8] which argues that a crucial source of in-
suchasmagazines,keys,orflowers.Also,mostofthetime,in- ternal rewards for humans is the self-engagement in activities
fants engage in particular activities for their own sake, rather which require skills just above their current level. Thus, for
Csikszentmihalyi,exploratorybehaviorcanbeexplainedbyan
thanasstepstowardssolvingpracticalproblems.Thisisindeed
the essence of play. This suggests the existence of a kind of intrinsic motivation for reaching situations which represent a
intrinsic motivation system, as proposed by psychologists like learning challenge. Internal rewards are provided when a sit-
|     |     |     |     |     |     |     |     | uation which | was | previously | not | mastered | becomes | mastered |     |
| --- | --- | --- | --- | --- | --- | --- | --- | ------------ | --- | ---------- | --- | -------- | ------- | -------- | --- |
White[5],whichprovideinternalrewardsduringtheseplayex-
periences.Suchinternalrewardsareobviouslyuseful,sincethey within an amount of time and effort which must not be too
areincentivestolearnmanyskillsthatwillpotentiallybereadily small but also not too large. Indeed, in analogy with Berlyne
[7],Csikszentmihalyiinsiststhattheinternalrewardismaximal
| available | later | on for challenges |     | and | tasks | which | are not yet |     |     |     |     |     |     |     |     |
| --------- | ----- | ----------------- | --- | --- | ----- | ----- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
foreseeable. whenthechallengeisnottooeasybutalsonottoodifficult.
Inordertodevelopinanopen-endedmanner,robotsshould
B. Neuroscience
| certainly | be equipped |     | with | capacities | for | autonomous | and |     |     |     |     |     |     |     |     |
| --------- | ----------- | --- | ---- | ---------- | --- | ---------- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
activedevelopment,and inparticularwithintrinsicmotivation Recentdiscoveriesshowingaconvergencebetweenpatterns
| systems, | forming | the | core of | a system | for | task-independent |     |             |     |              |          |     |         |              |     |
| -------- | ------- | --- | ------- | -------- | --- | ---------------- | --- | ----------- | --- | ------------ | -------- | --- | ------- | ------------ | --- |
|          |         |     |         |          |     |                  |     | of activity | in  | the midbrain | dopamine |     | neurons | and computa- |     |
learning.However,thiscrucialissueisstilllargelyunderinves- tionalmodelofreinforcementlearninghaveledtoanimportant
tigated.Therestofthispaperisorganizedinthefollowingway. amount of speculations about learning activities in the brain
| Section | II presents | a general |     | discussion | of  | research | related | to  |     |     |     |     |     |     |     |
| ------- | ----------- | --------- | --- | ---------- | --- | -------- | ------- | --- | --- | --- | --- | --- | --- | --- | --- |
[9].Centraltosomeofthesemodelsistheideathatdopamine
intrinsicmotivationinthedomainofpsychology,neuroscience, cells report the error in predicting expected reward delivery.
developmentalrobotics,andactivelearning.SectionIIIpresents Most experiments in this domain focus on the involvement of
| a critical | review | and a | classification |     | of existing | intrinsic | moti- |          |     |            |           |     |           |        |        |
| ---------- | ------ | ----- | -------------- | --- | ----------- | --------- | ----- | -------- | --- | ---------- | --------- | --- | --------- | ------ | ------ |
|            |        |       |                |     |             |           |       | dopamine | for | predicting | extrinsic | (or | external) | reward | (e.g., |
vationsystemsanddetermineskeycharacteristicsimportantto food). Yet recently, some researchers provided ground for the
permit autonomous mentaldevelopment.Section IV describes idea that dopamine might also be involved in the processing
indetailthealgorithmofIntelligentAdaptiveCuriosity(IAC).
|     |     |     |     |     |     |     |     | of types | of intrinsic | motivation |     | associated | with | novelty | and |
| --- | --- | --- | --- | --- | --- | --- | --- | -------- | ------------ | ---------- | --- | ---------- | ---- | ------- | --- |
Section V discusses methodological issues for characterizing exploration [10], [11]. In particular, some studies suggest that
the behavior and performances of such systems. Section VI dopamineresponses couldbe interpretedasreporting“predic-
| presents | a first | experiment | using | IAC | with | a simple | simulated |     |     |     |     |     |     |     |     |
| -------- | ------- | ---------- | ----- | --- | ---- | -------- | --------- | --- | --- | --- | --- | --- | --- | --- | --- |
tionerror”(andnotonly“rewardpredictionerror”)[12].These
robot. Section VII presents a second more complex experi- findings support the idea that intrinsic motivation systems
mentinvolvingaphysicalrobotdiscoveringaffordancesabout couldbepresentinthebraininsomeformoranotherandthat
| entities                                             | in its environment. |     | Section |     | VIII discusses |     | the results |              |           |            |       |       |      |            |         |
| ---------------------------------------------------- | ------------------- | --- | ------- | --- | -------------- | --- | ----------- | ------------ | --------- | ---------- | ----- | ----- | ---- | ---------- | ------- |
|                                                      |                     |     |         |     |                |     |             | signals      | reporting | prediction | error | could | play | a critical | role in |
| obtainedinthesetwoexperimentsinrelationtomorecomplex |                     |     |         |     |                |     |             | thiscontext. |           |            |       |       |      |            |         |
issuesassociatedwithbehavioralorganizationandobservation
ininfantdevelopment.
C. DevelopmentalRobotics
Giventhisbackground,awaytoimplementanintrinsicmoti-
II. BACKGROUND vationsystemmightbetobuildamechanismwhichcanevaluate
operationallythedegreeof“novelty,”“surprise,”“complexity,”
A. Psychology1 or “challenge” that different situations provide from the point
ofviewofalearningrobot,andthendesigninganassociatedre-
White [5] presents an argumentation explaining why basic wardideallybeingmaximalwhenthesefeaturesareinaninter-
formsofmotivationsuchasthoserelatedtotheneedforfood,
mediatelevel,asproposedbyBerlyne[7]andCsikszentmihalyi
| sex, or | physical | integrity | maintenance |     | cannot | account | for an |     |     |     |     |     |     |     |     |
| ------- | -------- | --------- | ----------- | --- | ------ | ------- | ------ | --- | --- | --- | --- | --- | --- | --- | --- |
[13].Autonomousandactiveexploratorybehaviorcanthenbe
animal’sexploratorybehavior,particularlyinhumans.Hepro- achievedbyactingsoastoreachsituationswhichmaximizethis
| posed rather | that        | exploratory |             | behaviors | can  | be by | themselves |          |         |               |     |            |            |        |     |
| ------------ | ----------- | ----------- | ----------- | --------- | ---- | ----- | ---------- | -------- | ------- | ------------- | --- | ---------- | ---------- | ------ | --- |
|              |             |             |             |           |      |       |            | internal | reward. | The challenge |     | is to find | a sensible | manner | to  |
| a source     | of rewards. | Some        | experiments |           | have | been  | conducted  |          |         |               |     |            |            |        |     |
operationalizetheconceptsbehindthewords“novelty,”“com-
showing that exploration for its own sake is an activity which plexity,”“surprise,”or“challenge”whichareonlyverballyde-
| is not always | a   | secondary | reinforcer, |     | it is | certainly | a built-in |     |     |     |     |     |     |     |     |
| ------------- | --- | --------- | ----------- | --- | ----- | --------- | ---------- | --- | --- | --- | --- | --- | --- | --- | --- |
scribedandoftenvaguelydefinedinthepsychologyliterature.
| primary | reinforcer. | The | literature | on  | education | and | develop- |     |     |     |     |     |     |     |     |
| ------- | ----------- | --- | ---------- | --- | --------- | --- | -------- | --- | --- | --- | --- | --- | --- | --- | --- |
Onlyafewresearchershavesuggestedsuchimplementations,
mentalsostressesthedistinctionbetweenintrinsicandextrinsic andevenfewerhavetestedthemonrealrobots.Typically,they
| motivations | [6].  | Psychologists |          | have | proposed    | possible | mech- |            |         |               |     |     |        |             |     |
| ----------- | ----- | ------------- | -------- | ---- | ----------- | -------- | ----- | ---------- | ------- | ------------- | --- | --- | ------ | ----------- | --- |
|             |       |               |          |      |             |          |       | call these | systems | of autonomous |     | and | active | exploratory | be- |
| anisms      | which | explain       | the kind | of   | exploratory | behavior | that, |            |         |               |     |     |        |             |     |
havior“artificialcuriosity.”Schmidhuberetal.[14],Thrun[15],
for example, humans show. Berlyne [7] proposed that explo- and Herrmann et al. [16] provided initial implementations of
rationmightbetriggeredandrewardedforsituationswhichin-
artificialcuriosity,buttheydidnotintegratethisconceptwithin
cludenovelty,surprise,incongruity,andcomplexity.Healsore-
theproblematicofdevelopmentalrobotics,inthesensethatthey
finedthisideabyobservingthatthemostrewardingsituations were not concerned with the emergent development sequence
1Thereviewofthepsychologyandneuroscienceliteratureinthissectionis andwiththeincreaseofthecomplexityoftheirmachines(and
partlyinspiredfromBartoetal.([21]). theydidnotuserobots,butlearningmachinesonsomeabstract

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 267
problems). They were only concerned in how far artificial cu- robotsintheseapproachescanbedescribedashavingtwomod-
riositycanspeeduptheacquisitionofknowledge.Thefirstin- ules: 1) one module implements a learning machine M which
tegratedviewofdevelopmentalroboticsthatincorporatedapro- learns to predict the sensorimotor consequences when a given
posalforanoveltydrivewasdescribedbyWengandcolleagues
actionisexecutedinagivensensorimotorcontextand2)another
[17],[18].Then,KaplanandOudeyerproposedanimplemen- moduleisameta-learningmachinemetaMwhichlearnstopre-
tationofartificialcuriositywithinadevelopmentalframework dict the errors that machine M makes in its predictions: these
[19],andMarshalletal.,aswellasBartoetal.presentedvaria-
meta-predictionsarethenusedasthebasisofameasureofthe
tionsonthenoveltydrive[20],[21].Aswewillexplainlateron potential interestingness of a given situation. The existing ap-
inthispaper,thesepioneeringsystemshaveanumberoflimi-
proachescanbedividedintothreegroups,accordingtotheway
tationsmakingthemimpossibletouseonrealrobotsinrealun-
action-selectionismadedependingonthepredictionsofMand
| controlledenvironments.Furthermore,toourknowledge,ithas |     |     |     |     |     |     |     | metaM. |     |     |     |     |     |     |     |
| ------------------------------------------------------- | --- | --- | --- | --- | --- | --- | --- | ------ | --- | --- | --- | --- | --- | --- | --- |
notyetbeenshownhowtheycouldsuccessfullyleadtotheau-
| tonomous  | formation | of     | a developmental |      | sequence  | comprising |          |                             |             |        |       |       |           |       |            |
| --------- | --------- | ------ | --------------- | ---- | --------- | ---------- | -------- | --------------------------- | ----------- | ------ | ----- | ----- | --------- | ----- | ---------- |
|           |           |        |                 |      |           |            |          | A. Group1:ErrorMaximization |             |        |       |       |           |       |            |
| more than | one       | stage. | This means      | that | typically | they       | have al- |                             |             |        |       |       |           |       |            |
|           |           |        |                 |      |           |            |          | In the                      | first group | (e.g., | [15], | [18], | [20], and | [21]) | robots di- |
lowedforthedevelopmentandemergenceofonelevelofbehav-
|     |     |     |     |     |     |     |     | rectly use | the | error predicted |     | by metaM | to  | choose | which ac- |
| --- | --- | --- | --- | --- | --- | --- | --- | ---------- | --- | --------------- | --- | -------- | --- | ------ | --------- |
ioralpatterns,butdidnotshowhownewlevelsofmorecomplex
tiontodo.2Theactionthattheychooseateachstepistheone
behavioralpatternscouldemergewithouttheinterventionofa
forwhichmetaMpredictsthelargesterrorinpredictionofM.
humanorachangeintheenvironmentprovokedbyahuman.
ThishasshowntobeefficientwhenthemachineMhastolearn
|     |     |     |     |     |     |     |     | a mapping | which | is learnable, |     | deterministic, |     | and | with homo- |
| --- | --- | --- | --- | --- | --- | --- | --- | --------- | ----- | ------------- | --- | -------------- | --- | --- | ---------- |
D. ActiveLearning
|     |     |     |     |     |     |     |     | geneous | Gaussian | noise | [15], | [17], | [21], [32]. | However, | this |
| --- | --- | --- | --- | --- | --- | --- | --- | ------- | -------- | ----- | ----- | ----- | ----------- | -------- | ---- |
Interestingly, the mechanisms developed in these papers methodshowslimitationswhenusedinarealuncontrolleden-
devotedtotheimplementationofartificialcuriosityhavestrong vironment. Indeed, in such a case, the mapping that M has to
similarities with mechanisms developed in the field of statis- learnisnolongerdeterministic,andthenoiseisvastlyinhomo-
| tics, under | the | term “optimal |     | experiment | design” | [22], | and | in  |     |     |     |     |     |     |     |
| ----------- | --- | ------------- | --- | ---------- | ------- | ----- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
geneous.Practically,thismeansthatarobotusingthismethod
machine learning, under the term “active learning” [23], [24]. will, for example, be stuck by white noise or, more generally,
Inthesecontexts,theproblemissummarizedwiththequestion: bysituationswhichareinherentlytoocomplexforitslearning
| How to | choose | the next | example | for | a learning | machine |     | in        |               |     |           |     |        |           |         |
| ------ | ------ | -------- | ------- | --- | ---------- | ------- | --- | --------- | ------------- | --- | --------- | --- | ------ | --------- | ------- |
|        |        |          |         |     |            |         |     | machinery | or situations |     | for which | the | causal | variables | are not |
ordertominimizethenumberofexamplesnecessarytoachieve perceivable or observable by the robot. For example, a robot
agivenlevelofperformanceingeneralization?Orsaidanother
equippedwithadrivewhichpushesittowardssituationswhich
| way: How | to choose | the | next | example | so  | that the gain | in in- |               |     |               |     |       |          |          |         |
| -------- | --------- | --- | ---- | ------- | --- | ------------- | ------ | ------------- | --- | ------------- | --- | ----- | -------- | -------- | ------- |
|          |           |     |      |         |     |               |        | are maximally |     | unpredictable |     | might | discover | and stay | focused |
formationforthemachinelearnerwillbemaximal?Anumber on movement sequences like running fast against a wall, the
oftechniquesdevelopedinactivelearninghavebeenprovento
|     |     |     |     |     |     |     |     | shock resulting |     | in an | unpredictable |     | bounce | (in principle, | the |
| --- | --- | --- | --- | --- | --- | --- | --- | --------------- | --- | ----- | ------------- | --- | ------ | -------------- | --- |
speedupsignificantlythelearningofmachines(e.g.,[25]–[31])
|     |     |     |     |     |     |     |     | bounce | is predictable |     | since it | obeys | the deterministic |     | laws of |
| --- | --- | --- | --- | --- | --- | --- | --- | ------ | -------------- | --- | -------- | ----- | ----------------- | --- | ------- |
andeventoallowperformanceongeneralizationwhicharenot
|     |     |     |     |     |     |     |     | classic mechanics |     | but, | in practice, | this | prediction |     | requires the |
| --- | --- | --- | --- | --- | --- | --- | --- | ----------------- | --- | ---- | ------------ | ---- | ---------- | --- | ------------ |
possiblewithpassivelearning[32].Yet,thesetechniqueswere
|     |     |     |     |     |     |     |     | perfect | knowledge | of  | all the | physical | properties |     | of the robot |
| --- | --- | --- | --- | --- | --- | --- | --- | ------- | --------- | --- | ------- | -------- | ---------- | --- | ------------ |
developedforapplicationsinwhichthemappingtobelearned
|                 |     |           |           |           |              |                 |     | body, as  | well as | those         | of the | wall, which     | is  | typically     | far from |
| --------------- | --- | --------- | --------- | --------- | ------------ | --------------- | --- | --------- | ------- | ------------- | ------ | --------------- | --- | ------------- | -------- |
| was clean       | and | typically | presented | as        | preprocessed | well-pre-       |     |           |         |               |        |                 |     |               |          |
|                 |     |           |           |           |              |                 |     | being the | case    | for a robot). | So,    | in uncontrolled |     | environments, |          |
| pared datasets. |     | They are  | also      | typically | based        | on mathematical |     |           |         |               |        |                 |     |               |          |
arobotequippedwiththisintrinsicmotivationsystemwillget
| theory like | Optimal | Experiment |     | Design | which | assumes | that |     |     |     |     |     |     |     |     |
| ----------- | ------- | ---------- | --- | ------ | ----- | ------- | ---- | --- | --- | --- | --- | --- | --- | --- | --- |
stuckanddisplaybehaviorswhichdonotleadtodevelopment
| the noise | is independently |     | normally |     | distributed | [33]. | On the |     |     |     |     |     |     |     |     |
| --------- | ---------------- | --- | -------- | --- | ----------- | ----- | ------ | --- | --- | --- | --- | --- | --- | --- | --- |
andthatcansometimesevenbedangerous.
| contrary,          | the domain |        | that real | robots | shall    | investigate | is the  |                                |     |     |     |     |     |     |     |
| ------------------ | ---------- | ------ | --------- | ------ | -------- | ----------- | ------- | ------------------------------ | --- | --- | --- | --- | --- | --- | --- |
| real unconstrained |            | world, | which     | is     | a highly | complicated | and     |                                |     |     |     |     |     |     |     |
|                    |            |        |           |        |          |             |         | B. Group2:ProgressMaximization |     |     |     |     |     |     |     |
| “muddy”            | structure, | as     | pointed   | out by | Weng     | [34], full  | of very |                                |     |     |     |     |     |     |     |
Asecondgroupofmodelstriedtoavoidgettingstuckinthe
| different | kinds | of intertwined |     | non-Gaussian |     | inhomogeneous |     |     |     |     |     |     |     |     |     |
| --------- | ----- | -------------- | --- | ------------ | --- | ------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
noise.Asaconsequence,thesemethodscannotbeuseddirectly presenceofpurenoiseorunlearnablesituationsbyusingmore
inthedevelopmentalroboticsdomain,andthereisnoobvious indirectlythepredictionoftheerrorofM(e.g.,[16]and[19]).
|     |     |     |     |     |     |     |     | In these | models, | a third | module | that | we call | knowledge | gain |
| --- | --- | --- | --- | --- | --- | --- | --- | -------- | ------- | ------- | ------ | ---- | ------- | --------- | ---- |
waytoextendtheminthisdirection.Moreover,thereexistsno
efficient implementation for methods like optimal experiment assessor (KGA) is added to the architecture. Fig. 1 shows an
designincontinuousspaces,andalreadyindiscretespacesthe illustrationofthesesystems.Thisnewmoduleenhancestheca-
computationalcostishigh[35]. pabilitiesofthemeta-machinemetaM:KGApredictsthemean
errorrateofMintheclosefutureandinthenextsensorimotor
contexts.KGAalsostorestherecentmeanerrorrateofMinthe
| III. | EXISTINGINTRINSICMOTIVATIONSYSTEMS |     |     |     |     |     |     |             |              |     |           |     |             |       |          |
| ---- | ---------------------------------- | --- | --- | --- | --- | --- | --- | ----------- | ------------ | --- | --------- | --- | ----------- | ----- | -------- |
|      |                                    |     |     |     |     |     |     | most recent | sensorimotor |     | contexts. |     | The crucial | point | of these |
Existing computational approaches to intrinsic motivations models is that the interestingness of candidate situations are
and artificial curiosity are typically based on an architecture evaluatedusingthedifferencebetweentheexpectedmeanerror
whichcomprisesamachinewhichlearnstoanticipatetheconse- rate of the predictions of M in the close future, and the mean
quencesoftherobot’sactions,andinwhichtheseactionsareac-
2Ofcourse,weareonlytalkingaboutthe“novelty”drivehere:theirrobots
tivelychosenaccordingtosomeinternalmeasuresrelatedtothe
aresometimesequippedwithothercompetingdrivesorcanrespondtoexternal
noveltyor predictability of the anticipated situation. Thus, the humanbasedrewardsources.

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
268 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
errorratetoactivitieswithalowermeanerrorrate,whichcanbe
higherthanthe rewards corresponding toan effectiveincrease
oftheskillsoftherobotinoneoftheactivities.Thiswillpush
therobottowardsinstablebehavior,inwhichitfocusesonthe
sudden shifts between different kinds of activities rather than
concentrateontheactualactivities.
C. Group3:Similarity-BasedProgressMaximization
Changes are needed so that methods based on the decrease
oftheerrorrateinpredictioncanstillworkinarealisticcom-
plexdevelopmentalroboticssetup.Itisnecessarythattherobot
monitorstheevolutionofitserrorrateinpredictioninsituations
which are of the same kind. It will no longer compare its cur-
renterrorratewithitserrorrateintherecentpast,whateverthe
currentsituationandthesituationintheclosepastare.Thesim-
ilaritybetweensituationsmustbetakenintoaccount.Building
Fig.1. Thearchitectureusedinvariousmodelsofgroup2andgroup3.Here,
asystemwhichcandothatcorrectlyrepresentsabigchallenge.
thereisamoduleKGAwhichmonitorsthederivativeoftheerrorsofprediction
ofM,whichisthebasisofanevaluationoflearningprogress.Somesystems Indeed,adevelopmentalrobotwillnotbegivenaninnatemech-
(group2)evaluatethelearningprogressbymeasuringthedecreaseoftheerror anism with a preprogrammed set of kinds of situations and a
rateofMintheclosepast,whatevertherecentsituations.Someothersystems
mechanism for categorizing each particular situation into one
(group3)evaluatethelearningprogressbymeasuringthedecreaseoftheerror
rateofMinsituationswhicharesimilar,butnotnecessarilycloseintime. ofthesekinds.Adevelopmentalrobothastobeabletobuildby
itselfameasureofthesimilarityofsituationsandultimatelyan
organizationoftheinfinitecontinuousspaceofparticularsitu-
errorrateintheclosepast.Foreachsituationthattheroboten- ations into higher level categories (or kinds) of situations. For
counters, it is given an internal reward which is equal to the example,adevelopmentalrobotdoesnotknowinitiallythaton
inverse of this difference (which also corresponds to the local the one hand, there can be the “gripping objects” kind of ac-
derivativeoftheerrorratecurveofM).Thisinternalrewardis tivityand,ontheotherhand,the“vocalizingtoothers”kindof
positivewhentheerrorratedecreases,andnegativewhenitin- activity.Initially,the worldisjustacontinuousstreamofsen-
creases.Themotivationsystemoftherobotisthenasystemin sationsandlow-levelmotorcommandsfortherobot.
whichtheactionchosenisthatforwhichKGApredictsthatit Arelatedapproach,butwithanactivelearningpointofview
will lead to the greatest decrease of the mean error rate of M. rather than a developmental robotics point of view, was pro-
Thisensuresthattherobotwillnotstayinfrontofwhitenoise posed in [14] and presented an implementation of the idea of
foralongtimeorinunlearnablesituationsbecausethisdoesnot evaluating the learning progress by monitoring the evolution
leadtoadecreaseofitserrorsinprediction. of the error rate in similar situations. The implementation de-
However,thismethodhasonlybeentestedinspacesinwhich scribedwastestedfordiscreteenvironmentslikeatwo-dimen-
therobotcandoonlyonekindofactivity,suchas,forexample, sionalgridvirtualworldonwhichanagentcouldmoveanddo
movingtheheadandlearningtopredictthepositionofhighlu- oneoffourdiscreteactions.Thesimilarityoftwosituationswas
minancepoints[19].However,theidealcharacteristicofade- evaluatedbyabinaryfunctionstatingwhethertheycorrespond
velopmentalrobotisthatitmayengageinvariouskindsofac- exactlytothesamediscretestateornot.Fromanactivelearning
tivities, such as learning to walk, learning to grip things in its pointofview,itwasshownthatinthiscasethesystemcansig-
hand,learningtotrackavisualtarget,learningtocatchtheat- nificantlyspeedupthelearning,evenifsomepartsofthespace
tentionofothersocialbeings,learningtovocalize,etc.Insuch arepurenoise.Thissystemwasnotstudiedunderthedevelop-
cases,therobotcantypicallyswitchrapidlyfromoneactivityto mental robotics point of view: it was not shown whether this
theother,forexample,makingatrialatgrippinganobjectthat allowedforaself-organizationofthebehavioroftherobotinto
it sees and suddenly shifting to trying to track the movement adevelopmentalsequencefeaturingclearlyseveralstagesofin-
ofanotherbeinginitsenvironment.Insuchacase,measuring creasing complexity. Moreover, because the system was only
theevolutionintimeofitsperformanceinpredictingwhathap- testedonadiscretesimulatedenvironment,itisdifficulttogen-
pens will lead to a measure which is hardly interpretable. In- eralizetheresultstoreal-worldconditionswithcontinuousen-
deed,usingthemethodwedescribedinthelastparagraphwill vironmentandactionspaces,andwheretwosituationsarenever
maketherobotcompareitserrorrateinanticipationwhileitis numericallyexactlythesame.Nevertheless,thispapersuggests
tryingtogripanobject,withitserrorrateinanticipationwhile apossiblemannertousethismethodincontinuousspaces.Itis
itistryingtoanticipatethereactionoftheotherbeingwhenhe based on the use of a learning machine such as a feedforward
vocalizes,ifthesetwokindsofactivitiesaresequenced.Thus, neural network which takes as input a particular situation and
itwilloftenleadtherobottocompareitsperformancesbetween predictstheerrorassociatedwiththeanticipationoftheconse-
activities which are of a different kind, which has no obvious quenceofagivenactioninthissituation.Thismeasureisthen
meaning.Andindeed,usingthisdirectmeasureofthedecrease used in a formula to evaluate the learning progress. Thanks to
in the error rate in prediction will provide the robot with in- thegeneralizationpropertiesofamachinelikeaneuralnetwork,
ternalrewardswhenshiftingfromanactivitywithahighmean theauthorclaimsthatthemechanismwillcorrectlygeneralize

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 269
theevaluationoflearningprogressfromonesituationtosimilar and from situations which are too unpredictable (i.e., the
situations.However,itisnotclearhowthiswillworkinpractice edgeoforderandchaosinthecognitivedynamics).Indeed,
sincetheerrorfunction,andthusthelearningprogressfunction, thankstothefactthatoneevaluatesthelearningprogress
are locally highly nonstationary. This provokes a risk of over- by comparing situations which are similar and in a “re-
generalization.Anotherlimitofthisworkresideswithinthepar- gional”manner,thepathologicbehaviorsthatwedescribed
ticularformulathatisusedtoevaluatethelearningprogressas- intheprevioussectionareavoided.
sociatedwithacandidatesituation,whichconsistsinmakingthe We will now describe how this system can be fully imple-
differencebetweentheerrorintheanticipationofthissituation mented.Thisimplementationcanbe variedinmanyways, for
beforeithasbeenexperiencedandtheerrorintheanticipation example, by replacing the implementation of the learning ma-
ofexactlythesamesituationafterithasbeenexperienced.On chinesM,metaM,andKGA.Theoneweprovideisbasicand
theonehand,thiscanonlyworkforalearningmachinewitha wasdevelopedforitspracticalefficiency.Also,itwillbeclear
lowlearningrate,aspointedoutbytheauthor,andwillnotwork tothe readerthatinan efficientimplementation,the machines
with,forexample,one-shotlearningofmemory-basedmethods. M, metaM, and KGA are not easily separable (keeping them
Ontheotherhand,consideringthestateofthelearningmachine separateentities inthe previousparagraphswasforreasonsof
justbeforeandjustafteronesingleexperiencecanpossiblybe keepingtheexplanationeasiertounderstand).
sensitivetostochasticfluctuations.
A. Summary
| The next | section        | will | present |      | a system      | which | provides     |     |     |     |     |     |     |     |
| -------- | -------------- | ---- | ------- | ---- | ------------- | ----- | ------------ | --- | --- | --- | --- | --- | --- | --- |
| another  | implementation |      | of the  | idea | of evaluating |       | the learning |     |     |     |     |     |     |     |
IACreliesonamemorywhichstoresalltheexperiencesen-
progressbycomparingsimilarsituations.Thissystemismade counteredbytherobotintheformofvectorexemplars.Thereis
| to work | in continuous |     | spaces, | and | we will | actually | show that |     |     |     |     |     |     |     |
| ------- | ------------- | --- | ------- | --- | ------- | -------- | --------- | --- | --- | --- | --- | --- | --- | --- |
amechanismwhichincrementallysplitsthesensorimotorspace
| this system | works | both | in a | virtual | robot | setup and | in a real |     |     |     |     |     |     |     |
| ----------- | ----- | ---- | ---- | ------- | ----- | --------- | --------- | --- | --- | --- | --- | --- | --- | --- |
intoregions,basedontheseexemplars.Eachregionischaracter-
robotic setup withcontinuous motor and/or perceptual spaces. izedbyitsexclusivesetofexemplarsandisalsoassociatedwith
Oneofitscrucialfeaturesisthatitintroducesamechanismof
itsownlearningmachine,whichwecallanexpert.Thisexpert
| situation | categorization, |     | which | splits | the space | incrementally |     |     |     |     |     |     |     |     |
| --------- | --------------- | --- | ----- | ------ | --------- | ------------- | --- | --- | --- | --- | --- | --- | --- | --- |
istrainedwiththeexemplarsavailableinitsregion.Whenapre-
| and autonomously |     | into | different | regions, | which | correspond |     | to  |     |     |     |     |     |     |
| ---------------- | --- | ---- | --------- | -------- | ----- | ---------- | --- | --- | --- | --- | --- | --- | --- | --- |
dictioncorrespondingtoagivensituationhastobemadebythe
differentkindsofactivitiesfromthepointofviewoftherobot.
robot,thentheexpertoftheregionwhichcoversthissituation
This allows us to compare the similarity of two situations not is picked up and used for the prediction. Each time an expert
| directly | based | on their | intrinsic | metric | distance, |     | but on their |     |     |     |     |     |     |     |
| -------- | ----- | -------- | --------- | ------ | --------- | --- | ------------ | --- | --- | --- | --- | --- | --- | --- |
makesapredictionassociatedwithanactionwhichisactually
| belonging | toa | givensituation |     | category.Anotherfeature |     |     | is the |     |     |     |     |     |     |     |
| --------- | --- | -------------- | --- | ----------------------- | --- | --- | ------ | --- | --- | --- | --- | --- | --- | --- |
executed,itserrorinpredictionismeasuredandstoredinalist
factthatwemonitorineachoftheseregionstheevolutionofthe
|            |               |     |        |          |        |     |             | which is | associated | to  | its region. Each | region | has | its own list. |
| ---------- | ------------- | --- | ------ | -------- | ------ | --- | ----------- | -------- | ---------- | --- | ---------------- | ------ | --- | ------------- |
| error rate | in prediction |     | for an | extended | period | of  | time, which |          |            |     |                  |        |     |               |
Thislistisusedtoevaluatethepotentiallearningprogressthat
allowsustousesmoothingproceduresandavoidproblemsdue canbegainedbygoinginasituationcoveredbyitsassociated
| to stochastic | fluctuations. |     | The | “regional” |     | evaluation | of simi- |     |     |     |     |     |     |     |
| ------------- | ------------- | --- | --- | ---------- | --- | ---------- | -------- | --- | --- | --- | --- | --- | --- | --- |
region.Thisismadebasedonasmoothingofthelistoferrors,
laritycombinedwiththesmoothingoftheerrorratecurveisa
andonanextrapolationofthederivative.Wheninagivensitu-
| way to cope | with | the | nonstationarity |     | of the | learning | progress |     |     |     |     |     |     |     |
| ----------- | ---- | --- | --------------- | --- | ------ | -------- | -------- | --- | --- | --- | --- | --- | --- | --- |
ation,therobotcreatesalistofpossibleactionsandchoosesthe
| function. | Another | feature | is  | that | it makes | no presupposition |     |     |     |     |     |     |     |     |
| --------- | ------- | ------- | --- | ---- | -------- | ----------------- | --- | --- | --- | --- | --- | --- | --- | --- |
oneforwhichitevaluates,itwillleadtoasituationwithmax-
on the learning rate of the learning machines, and thus can be imalexpectedlearningprogress.3
| used with | one-shot | learning |     | methods | like | nearest-neighbors |     |     |     |     |     |     |     |     |
| --------- | -------- | -------- | --- | ------- | ---- | ----------------- | --- | --- | --- | --- | --- | --- | --- | --- |
algorithms,aswellaswithslowlylearningneuralnetworksfor
B. SensorimotorApparatus
example.
|     |     |     |     |     |     |     |     | The robot | has | a number | of real-valued | sensors |     | , which |
| --- | --- | --- | --- | --- | --- | --- | --- | --------- | --- | -------- | -------------- | ------- | --- | ------- |
IV. INTELLIGENTADAPTIVECURIOSITY(IAC) are here summarized by the vector . Its actions are con-
|            |     |           |     |              |     |        |             | trolled by | the setting | of  | the real number | values       | of  | a set of ac- |
| ---------- | --- | --------- | --- | ------------ | --- | ------ | ----------- | ---------- | ----------- | --- | --------------- | ------------ | --- | ------------ |
| The system |     | described | in  | this section | is  | called | Intelligent |            |             |     |                 |              |     |              |
|            |     |           |     |              |     |        |             | tion/motor | parameters  |     | , which         | we summarize |     | using the    |
AdaptiveCuriosity(IAC).
|     |     |     |     |     |     |     |     | vector | . These | action | parameters | can | potentially | be very |
| --- | --- | --- | --- | --- | --- | --- | --- | ------ | ------- | ------ | ---------- | --- | ----------- | ------- |
(cid:127) Itisamotivation,ordrive,inthesamesensethatfoodlevel
lowlevel(forexample,thespeedofmotors)orofahigherlevel
maintenanceorheatmaintenancearedrives,butinsteadof
(forexample,thecontrolparametersofmotorprimitivessuchas
beingaboutthemaintenanceofaphysicalvariable,theIAC
thebitingorbashingmovementthatwewilldescribeinthesec-
driveisaboutthemaintenanceofanabstractdynamiccog-
|     |     |     |     |     |     |     |     | tion devoted | to  | the “Playground | Experiment”). |     | We  | denote the |
| --- | --- | --- | --- | --- | --- | --- | --- | ------------ | --- | --------------- | ------------- | --- | --- | ---------- |
nitivevariable:thelearningprogress,whichmustbekept
|     |     |     |     |     |     |     |     | sensorimotor | context |     | as the | vector | which | summarizes |
| --- | --- | --- | --- | --- | --- | --- | --- | ------------ | ------- | --- | ------ | ------ | ----- | ---------- |
maximal.Thisdefinitionmakesitanintrinsicmotivation.
thevaluesofallthesensorsandtheactionparametersattime
| (cid:127) It is | called | curiosity | because |     | maximizing |     | the learning |     |     |     |     |     |     |     |
| --------------- | ------ | --------- | ------- | --- | ---------- | --- | ------------ | --- | --- | --- | --- | --- | --- | --- |
progresspushes(asasideeffect)therobottowardsnovel 3Avariantofthissystemistheuseofonlyonemonolithiclearningsystem,
situationsinwhichthingscanbelearned. keepingthemechanismofregionconstructionbyincrementalspacesplitting.
Inthiscase,foreachpredictionofthesinglelearningsystem,itserrorisstored
| (cid:127) It is | adaptive | because |     | the situations |     | that are | attractive |     |     |     |     |     |     |     |
| --------------- | -------- | ------- | --- | -------------- | --- | -------- | ---------- | --- | --- | --- | --- | --- | --- | --- |
inthelistcorrespondingtotheregioncoveringtheassociatedsituation.The
change over time, indeed, once something is learned, it evaluationoftheexpectedlearningprogressofacandidatesituationisthesame
willnotprovidelearningprogressanymore. asinthesystempresentedhere.However,weprefertouseonelearningsystem
perregioninordertoavoidforgettingproblemswhicharetypicalofmonolithic
(cid:127) Itiscalledintelligentbecauseitkeeps,asasideeffect,the
learningmachineswhenusedinalifelonglearningsetupwithvariouskindsof
| robotawaybothfromsituationswhicharetoopredictable |     |     |     |     |     |     |     | situations. |     |     |     |     |     |     |
| ------------------------------------------------- | --- | --- | --- | --- | --- | --- | --- | ----------- | --- | --- | --- | --- | --- | --- |

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
270 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
[itistheconcatenationof and ].Inallthatfollows, findouttheexpertspecialistforthiscase:itistheoneforwhich
thereisaninternalclockintherobotwhichdiscretizesthetime, satisfiesallthecuttingtests(andthereisalwaysasingle
andnewactionsarechosenateverytimestep. expert,whichcorrespondstoeach ).
| C. Regions |     |       |      |          |     |         |           | D. Experts |        |         |       |            |          |         |     |
| ---------- | --- | ----- | ---- | -------- | --- | ------- | --------- | ---------- | ------ | ------- | ----- | ---------- | -------- | ------- | --- |
| IAC equips | the | robot | with | a memory | of  | all the | exemplars |            |        |         |       |            |          |         |     |
|            |     |       |      |          |     |         |           | To each    | region | , there | is an | associated | learning | machine |     |
whichhavebeenencounteredbytherobot.
|          |             |     |       |               |     |            |          | ,calledanexpert.Agivenexpert |     |       |     |      | isresponsibleforthe |              |     |
| -------- | ----------- | --- | ----- | ------------- | --- | ---------- | -------- | ---------------------------- | --- | ----- | --- | ---- | ------------------- | ------------ | --- |
| There is | a mechanism |     | which | incrementally |     | splits the | sensori- |                              |     |       |     |      |                     |              |     |
|          |             |     |       |               |     |            |          | predictionof                 |     | given |     | when |                     | isasituation |     |
motorspaceintoregions,basedontheseexemplars.Eachregion
|     |     |     |     |     |     |     |     | whichiscoveredbyitsassociatedregion |     |     |     |     |     | .Eachexpert |     |
| --- | --- | --- | --- | --- | --- | --- | --- | ----------------------------------- | --- | --- | --- | --- | --- | ----------- | --- |
ischaracterizedbyitsexclusivesetofexemplars.Atthebegin-
istrainedonthesetofexemplarswhichispossessedbyitsas-
| ning, there | is only | one | region | . Then, | when | a criterion |     |                |     |                                     |     |     |     |     |     |
| ----------- | ------- | --- | ------ | ------- | ---- | ----------- | --- | -------------- | --- | ----------------------------------- | --- | --- | --- | --- | --- |
|             |         |     |        |         |      |             |     | sociatedregion |     | .Anexpertcanbeaneural-network,asup- |     |     |     |     |     |
ismet,thisregionissplitintotworegions.Thisisdonerecur-
|                             |     |     |     |                         |     |     |     | port-vector | machine, | or  | a Bayesian | machine |     | for example. | For |
| --------------------------- | --- | --- | --- | ----------------------- | --- | --- | --- | ----------- | -------- | --- | ---------- | ------- | --- | ------------ | --- |
| sively.Averysimplecriterion |     |     |     | canbeused:whenthenumber |     |     |     |             |          |     |            |         |     |              |     |
alllearningmachineswhosetrainingcanbeincremental,such
| of exemplars | associated |     | with | the region | is  | above a | threshold |     |     |     |     |     |     |     |     |
| ------------ | ---------- | --- | ---- | ---------- | --- | ------- | --------- | --- | --- | --- | --- | --- | --- | --- | --- |
asneuralnetworks,support-vectormachines,ormemory-based
,thensplit.Thiscriterionallowsustoguaranteealow
methods,thenthesystemisefficientsinceitisnotnecessaryto
numberofexemplarsineachleaf,whichrenderstheprediction
retraineachexpertonalltheexemplarsofeachregion,butjust
andlearningmechanismthatwewilldescribecomputationally
|           |        |                  |     |     |             |         |         | toupdateone | singleexpertbyfeedingthe |     |     |     | newexemplartoit. |     |     |
| --------- | ------ | ---------------- | --- | --- | ----------- | ------- | ------- | ----------- | ------------------------ | --- | --- | --- | ---------------- | --- | --- |
| efficient | in the | next paragraphs. |     | The | counterpart | is that | it will |             |                          |     |     |     |                  |     |     |
Still,whenaregionissplit,onecannotusedirectlythe“parent”
| lead to systems |     | with many | regions | which | are | not easily | inter- |           |           |     |              |     |          |            |     |
| --------------- | --- | --------- | ------- | ----- | --- | ---------- | ------ | --------- | --------- | --- | ------------ | --- | -------- | ---------- | --- |
|                 |     |           |         |       |     |            |        | expert to | implement | the | two children |     | experts. | Each child | ex- |
pretablefromahumanpointofview.
pertistypicallyafreshexpertretrainedwiththeexemplarsthat
Whenasplittinghasbeendecided,thenanothercriterion
|     |     |     |     |     |     |     |     | its associatedregionhas |     |     | inherited.The |     | computationalcostas- |     |     |
| --- | --- | --- | --- | --- | --- | --- | --- | ----------------------- | --- | --- | ------------- | --- | -------------------- | --- | --- |
mustbeusedtofindouthowtheregionwillbesplit.Again,the
|                    |                |            |          |          |         |                    |         | sociatedwith | this  | is limited | due  | tothe | fact thatthe | number | of    |
| ------------------ | -------------- | ---------- | -------- | -------- | ------- | ------------------ | ------- | ------------ | ----- | ---------- | ---- | ----- | ------------ | ------ | ----- |
| choice of          | this criterion |            | was made | so       | that it | is computationally |         |              |       |            |      |       |              |        |       |
|                    |                |            |          |          |         |                    |         | exemplarsis  | never | higher     | than |       | asguaranteed |        | bythe |
| and experimentally |                | efficient. |          | The idea | is that | we split           | the set |              |       |            |      |       |              |        |       |
criterion.4
| of exemplars | into | two | sets so | that the | sum | of the variances | of  |     |     |     |     |     |     |     |     |
| ------------ | ---- | --- | ------- | -------- | --- | ---------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
componentsoftheexemplarsofeachset,weightedby
|     |     |     |     |     |     |     |     | E. EvaluationofLearningProgress |     |     |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- | --- | --- | ------------------------------- | --- | --- | --- | --- | --- | --- | --- |
thenumberofexemplarsofeachset,isminimal.Letusexplain
thismathematically.Letusdenote
Thispartitionofthesensorimotorspaceintodifferentregions
isthebasisofourregionalevaluationoflearningprogress.Each
timeanactionisexecutedbytherobotinagivensensorimotor
|                                    |     |     |     |     |     |              |     | context | coveredby |     | the | region | , the | robot can | mea- |
| ---------------------------------- | --- | --- | --- | --- | --- | ------------ | --- | ------- | --------- | --- | --- | ------ | ----- | --------- | ---- |
| thesetofexemplarspossessedbyregion |     |     |     |     |     | .Letusdenote |     | a       |           |     |     |        |       |           |      |
cuttingdimensionand anassociatedcuttingvalue.Then,the surethediscrepancybetweenthesensorystate thatthe
|                    |      |     |     |                   |     |     |      | expert | predictedandtheactualsensorystate |     |     |     |     |     | thatit |
| ------------------ | ---- | --- | --- | ----------------- | --- | --- | ---- | ------ | --------------------------------- | --- | --- | --- | --- | --- | ------ |
| splitof            | into | and |     | isdonebychoosinga |     |     | anda |        |                                   |     |     |     |     |     |        |
| suchthat(criterion |      |     | ):  |                   |     |     |      |        |                                   |     |     |     |     |     |        |
4Evencomputationallydemandinglearningmachinessuchasnonlinearsup-
(cid:127) alltheexemplars of havethe th port vector machines require only a few dozens milliseconds on a standard
computertobetrainedwith250examples,eveniftheseexampleshavesev-
| componentoftheir |     |     |     | smallerthan |     | ;   |     |     |     |     |     |     |     |     |     |
| ---------------- | --- | --- | --- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
eralhundreddimensions([36]).Intheexperimentsdescribedinthenextsec-
| (cid:127) alltheexemplars |     |     |     |     | of  | havethe |     | th           |                                                      |     |     |     |     |     |     |
| ------------------------- | --- | --- | --- | --- | --- | ------- | --- | ------------ | ---------------------------------------------------- | --- | --- | --- | --- | --- | --- |
|                           |     |     |     |     |     |         |     | tions,weusea | verysimplelearningalgorithmforimplementingtheexpert, |     |     |     |     |     |     |
componentoftheir greaterthan ; thenearest-neighborsalgorithm.Inthiscase,thereisnotevenaneedforre-
(cid:127) the quantity trainingtheexpert,sincetheexpertisthesetofexemplars.Ingeneral,theuse
ofthenearest-neighboralgorithmiscomputationallycostlywhenusedatthe
predictionstage,sinceitrequiresasmanycomputationsofdistancesasthere
|     |     |     |     |     |     |     |     | areexemplars.Again,thecriterionC |     |     |     | guaranteesthatthenumberofexem- |     |     |     |
| --- | --- | --- | --- | --- | --- | --- | --- | -------------------------------- | --- | --- | --- | ------------------------------ | --- | --- | --- |
plarsisalwayslowandallowsforafastcomputationoftheclosestexemplar.
Itisalsointerestingtonotethatifonewoulduseamonolithiclearningsystem
withonlyoneglobalexpert,whichisavariationofIACmentionedearlier,then
isminimal,where theuseofthenearest-neighborsalgorithmwouldsoonbecomecomputation-
allyveryexpensivesincealifelonglearningrobotcanaccumulatemillionsof
exemplars.Onthecontrary,usinglocalexpertstowhichaccessiscomputed
withatreeofcheapnumericalcomparisons(seeFig.2)allowsustocompute
approximatelycorrectglobalnearestneighborswithalogarithmiccomplexity
(O(log(N)))ratherthanwithalinearcomplexity(O(n)).Infact,usingatree
structurewithlocalexpertsnotonlyallowstospeedupthenearest-neighbors
where isasetofvectorsand denotesthecardinalof algorithm,butitalsoallowstoincreasetheperformancesingeneralization.In
. practice,thismeansthatthesystemwepresentinthispaper,whenused,forex-
ample,withthenearest-neighborsalgorithm,canupdateitself,aswellasmake
Then,recursivelyandforeachregion,ifthecriterion ismet, predictionswhenitalreadypossesses3000000exemplarsinafewmilliseconds
theregionissplitintotworegionswiththecriterion .Thisis onapersonalcomputer,sinceinthiscaseitrequiresabout17scalarcompar-
isons(depthofthecorrespondingbalancedtree)and250distancecomputation
illustratedinFig.2. betweenpoints.Admittedly,thisrequiresalotofmemory,butitisinteresting
Eachregionstoresallthecuttingdimensionsandthecutting tonotethatthecollectionof3000000exemplarscomposedof,forexample,20
values that were used in its generation, as well as in the gen- dimensions,whichwouldtakeapproximately34daysinthecaseoftherobots
presentedinthe“PlaygroundExperiment”section,wouldrequireabout230Mb
erationofitsparentexperts.Asaconsequence,whenapredic-
inmemory,whichismuchlessthanthecapacityofmosthandheldcomputers
| tionhastobemadeoftheconsequencesof |     |     |     |     |     | ,itiseasyto |     | nowadays. |     |     |     |     |     |     |     |
| ---------------------------------- | --- | --- | --- | --- | --- | ----------- | --- | --------- | --- | --- | --- | --- | --- | --- | --- |

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 271
Wecanthendefinetheactuallearningprogressas
(2)
Eventually,whenaregionissplitintotworegions,bothnew
|     |     |     |     |     |     |     | regions | inherit | the | list | of past | errors from | their | parent | region, |
| --- | --- | --- | --- | --- | --- | --- | ------- | ------- | --- | ---- | ------- | ----------- | ----- | ------ | ------- |
whichallowsthemtomakeevaluationoflearningprogressright
fromthetimeoftheircreation.
F. ActionSelection
Wenowhaveinplaceapredictionmachineryandamecha-
| Fig. | 2. The sensorimotor |     | space is iteratively | and recursively | split | into sub- |     |     |     |     |     |     |     |     |     |
| ---- | ------------------- | --- | -------------------- | --------------- | ----- | --------- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
nismwhichprovidesaninternalreward(positiveornegative)
| spaces,whichwecall“regions.”EachregionR |     |     |     | isresponsibleformonitoring |     |     |     |     |     |     |     |     |     |     |     |
| --------------------------------------- | --- | --- | --- | -------------------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
theevolutionoftheerrorrateintheanticipationoftheconsequencesofthe
robot’sactionsiftheassociatedcontextsarecoveredbythisregion.Thislistof
regionalerrorratesisusedforlearningprogressevaluation.
eachtimeanactionisperformedinagivencontext,depending
onhowmuchlearningprogresshasbeenachieved.5Thegoalof
measures.Thisprovidesameasureoftheerroroftheprediction theintrinsicallymotivatedrobotisthentomaximizetheamount
of attime ofinternalrewardthatitgets.Mathematically,thiscanbefor-
|     |     |     |     |     |     |     | mulated | as  | the maximization |     |     | of future | expected | rewards | (i.e., |
| --- | --- | --- | --- | --- | --- | --- | ------- | --- | ---------------- | --- | --- | --------- | -------- | ------- | ------ |
maximizationofthereturn),thatis
Thissquarederrorisaddedtothelistofpastsquarederrorsof
|     | ,whicharestoredinassociationtotheregion |     |     |     | .Wedenote |     |     |     |     |     |     |     |     |     |     |
| --- | --------------------------------------- | --- | --- | --- | --------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
this list
|     |     |     |     |     |     |     | where | (   |     | )isthediscountfactor,whichassignsless |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- | --- | ----- | --- | --- | ------------------------------------- | --- | --- | --- | --- | --- |
weighontherewardexpectedinthefarfuture.
|              |     |                                         |     |     |     |     | This    | formulation |             | corresponds |     | to a     | reinforcement | learning  |     |
| ------------ | --- | --------------------------------------- | --- | --- | --- | --- | ------- | ----------- | ----------- | ----------- | --- | -------- | ------------- | --------- | --- |
|              |     |                                         |     |     |     |     | problem |             | formulation | [37],       | and | thus the | techniques    | developed |     |
| Notethathere |     | denotesatimewhichisspecifictotheexpert, |     |     |     |     |         |             |             |             |     |          |               |           |     |
inthisfieldcanbeusedtoimplementanactionselectionmech-
| andnottotherobot,thismeansthat |     |     |     |     | mightcorrespond |     |     |     |     |     |     |     |     |     |     |
| ------------------------------ | --- | --- | --- | --- | --------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
anismwhichwillallowtherobottomaximizefutureexpected
| to  | the error made | by        | the expert | in an action            | performed |      | at                            |              |     |         |     |                  |          |     |         |
| --- | -------------- | --------- | ---------- | ----------------------- | --------- | ---- | ----------------------------- | ------------ | --- | ------- | --- | ---------------- | -------- | --- | ------- |
|     |                |           |            |                         |           |      | rewards                       | efficiently. |     | Indeed, | in  | reinforcement    | learning |     | models, |
|     | for the        | robot,and | thatno     | actions correspondingto |           | this |                               |              |     |         |     |                  |          |     |         |
|     |                |           |            |                         |           |      | acontrollerchooseswhichaction |              |     |         |     | totakeinacontext |          |     | based   |
expertwereperformedbytherobotsincethattime.Theselists
|     |     |     |     |     |     |     | on  | rewards | provided | by  | a critic. | Traditional | models | view | the |
| --- | --- | --- | --- | --- | --- | --- | --- | ------- | -------- | --- | --------- | ----------- | ------ | ---- | --- |
associatedtotheregionsarethenusedtoevaluatethelearning
criticasbeingexternaltotheagent.Suchsituationscorrespond
| progressthathasbeenachievedafteranaction |     |     |     |                           | hasbeen |     |                                |         |         |           |        |              |        |        |        |
| ---------------------------------------- | --- | --- | --- | ------------------------- | ------- | --- | ------------------------------ | ------- | ------- | --------- | ------ | ------------ | ------ | ------ | ------ |
|                                          |     |     |     |                           |         |     | to extrinsicallymotivatedforms |         |         |           |        | of learning. | Butthe | critic | can    |
| achievedinsensorycontext                 |     |     |     | ,leadingtoasensorycontext |         |     |                                |         |         |           |        |              |        |        |        |
|                                          |     |     |     |                           |         |     | as                             | well be | part of | the agent | itself | (as clearly  | argued | by     | Sutton |
.Thelearningprogressthathasbeenachievedthrough
|                                        |                 |     |                         |                 |               |     | and       | Barto            | [37, pp. | 51–54]). |     | As a consequence, |               | the algorithm |         |
| -------------------------------------- | --------------- | --- | ----------------------- | --------------- | ------------- | --- | --------- | ---------------- | -------- | -------- | --- | ----------------- | ------------- | ------------- | ------- |
| thetransitionfromthe                   |                 |     | context,coveredbyregion |                 |               |     | ,         |                  |          |          |     |                   |               |               |         |
|                                        |                 |     |                         |                 |               |     | described |                  | in this  | section  | can | be interpreted    | as a          | critic        | capable |
| tothecontextwithaperceptualvector      |                 |     |                         |                 | iscomputedas  |     |           |                  |          |          |     |                   |               |               |         |
|                                        |                 |     |                         |                 |               |     | of        | producing        | internal | rewards  |     | in order          | to guide      | the           | agent   |
| thesmoothedderivativeoftheerrorcurveof |                 |     |                         |                 | corresponding |     |           |                  |          |          |     |                   |               |               |         |
|                                        |                 |     |                         |                 |               |     | in        | its development. |          | Thus,    | any | existing          | reinforcement | learning      |         |
| to                                     | the acquisition | of  | its recent exemplars.   | Mathematically, |               | the |           |                  |          |          |     |                   |               |               |         |
techniquecanbeassociatedwiththeIACdrive.
computationinvolvestwosteps.
|           |                                          |     |     |     |     |     | One                                           | simple | example |       | would      | be to use | Watkins’         | -learning |      |
| --------- | ---------------------------------------- | --- | --- | --- | --- | --- | --------------------------------------------- | ------ | ------- | ----- | ---------- | --------- | ---------------- | --------- | ---- |
| (cid:127) | Themeanerrorrateinpredictioniscomputedat |     |     |     |     | and |                                               |        |         |       |            |           |                  |           |      |
|           |                                          |     |     |     |     |     | [38].Thealgorithmlearnsanaction-valuefunction |        |         |       |            |           |                  |           | ,es- |
|           |                                          |     |     |     |     |     | timating                                      | how    | good    | it is | to perform | a given   | action           | (         | in   |
|           |                                          |     |     |     |     |     | ourcontext)inagivencontextualstate            |        |         |       |            |           | ( inourcontext). |           |      |
“Good”actionsareexpectedtoleadtomorefuturerewards(e.g.,
morefuturelearningprogressinourcontext).Thealgorithmcan
bedescribedinthefollowingproceduralform:
|     |       |                                           |     |     |     |     | (cid:127) | Initialize    |           |         | withsmallrandomuniformvalues; |                         |     |         |       |
| --- | ----- | ----------------------------------------- | --- | --- | --- | --- | --------- | ------------- | --------- | ------- | ----------------------------- | ----------------------- | --- | ------- | ----- |
|     |       |                                           |     |     |     |     | (cid:127) | Repeat        |           |         |                               |                         |     |         |       |
|     |       |                                           |     |     |     |     |           | — Insituation |           | ,choose |                               | usingapolicyderivedfrom |     |         | .     |
|     |       |                                           |     |     |     |     |           | For           | instance, | choose  |                               | that maximize           |     | in most | cases |
|     | where | isatimewindowparametertypicallyequalto15, |     |     |     |     |           |               |           |         |                               |                         |     |         |       |
and asmoothingparametertypicallyequalto25. 5Tointegraterewardresultingfromlearningprogresswithotherkindsof(pos-
(cid:127) Theactualdecreaseinthemeanerrorrateinpredictionis siblyextrinsic)rewards,aweightedsumcanbeused.Aparameter(cid:11) specifies
|     | defined as |     |     |     |     |     | therelativeweightofeachrewardtype |     |     |     |       |                        |     |     |     |
| --- | ---------- | --- | --- | --- | --- | --- | --------------------------------- | --- | --- | --- | ----- | ---------------------- | --- | --- | --- |
|     |            |     |     |     |     |     |                                   |     |     |     | r(t)= | (cid:11) (cid:1)r (t): |     |     |     |
(1)

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
272 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
but every once in a while, with a probability instead (cid:127) The action for which the system expects the maximal
selectanactionatrandom,uniformly(thisiscalledan learning progress is chosen and executed except in some
-greedyactionselectionrule[37]); caseswhenarandomactionisselected( –greedyaction
— Performaction ,observe ,andtheresultingstate ; selectionrule).Inthefollowingexperiments istypically
| —   |     |     |     |     |     | 0.35. |     |     |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- | ----- | --- | --- | --- | --- | --- | --- | --- |
; (cid:127) After the action has been executed and the consequences
| —                 | ;                                   |     |     |     |     | measured,thesystemisupdated. |                |     |        |     |           |     |     |
| ----------------- | ----------------------------------- | --- | --- | --- | --- | ---------------------------- | -------------- | --- | ------ | --- | --------- | --- | --- |
| wheretheparameter | isthelearningratecontrollinghowfast |     |     |     |     |                              |                |     |        |     |           |     |     |
|                   |                                     |     |     |     |     | V.                           | METHODOLOGICAL |     | ISSUES |     | MEASURING |     |     |
the action-value functionis updated byexperience.Of course, FOR
all the complex issues traditionally encountered in reinforce- BEHAVIORALCOMPLEXITY
mentlearningliketradeoffbetweenexplorationandexploitation
Fromadevelopmentalroboticspointofview,intrinsicmoti-
staycrucialforsystemsusinginternalrewardsbasedonintrinsic vationsystemsareinterestingasawaytoachieveacontinuous
motivation.
increaseinbehavioralcomplexity.Thisraisesissuesforfinding
Thepurposeofthispaperistofocusonthestudyandunder- adequate methods to evaluate such systems. Evaluation based
standing of the learning progress definition that we presented. on performance level for a set of predefined tasks is the most
| Using a complex | reinforcement | machinery | brings | complexity |     |     |     |     |     |     |     |     |     |
| --------------- | ------------- | --------- | ------ | ---------- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
commonwaytoassessthelearningprogressofadaptiverobots.
andbiaseswhicharespecifictoaparticularmethod,especially However,asintrinsicmotivationsystemsaredesignedtoresult
concerningthewaytheyprocessdelayedrewards.Whileusing
intask-independentautonomousdevelopment,usinganevalu-
suchamethodwithintrinsicmotivationsystemswillsurelybe ation paradigm coming from task-oriented design is not well
usefulinthefuture,andis,infact,anentiresubjectofresearch adapted.Moreover,suchevaluationmethodsareassociatedwith
asillustratedbytheworkofBartoetal.[21]whohavestudied
thetemptinganthropomorphicbiastoevaluatehowwellrobots
theuseofsophisticatedreinforcementlearningtechniquesona managetolearnthetasksthathumanscanlearn.
simplenovelty-basedintrinsicmotivationsystem,wewillnow The issue is therefore to evaluate the increase of a robot’s
makeasimplificationwhichwillallowusnottousesuchsophis-
|     |     |     |     |     |     | behavioral | complexity | during | a developmental |     | sequence. |     | It is |
| --- | --- | --- | --- | --- | --- | ---------- | ---------- | ------ | --------------- | --- | --------- | --- | ----- |
ticated reinforcement learning methods so that the results we important to stress that there is not a single objective way for
| will presentin | the experimentsectioncan |     | be interpretedmore |     |     |     |     |     |     |     |     |     |     |
| -------------- | ------------------------ | --- | ------------------ | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
assessingtheincreaseofcomplexityofasystem.Complexityis
easily.Indeed,thisisanecessarystepsinceourintrinsicmoti- always related toa givenobserver[39]. Threecomplementary
vationsysteminvolvesanontrivialmeasureoflearningprogress approachescanbeenvisioned.
whichmustbecarefullyunderstood.Thissimplificationconsists
|     |     |     |     |     |     | (cid:127) First, | it is possible | to evaluate | the | increase | in  | complexity |     |
| --- | --- | --- | --- | --- | --- | ---------------- | -------------- | ----------- | --- | -------- | --- | ---------- | --- |
inhavingthesystemtrytomaximizeonlytheexpectedreward from the robot’s point of view. This means measuring
| itwillreceiveat | ,i.e., |     | .Thispermitsustoavoid |     |     |          |           |              |     |         |                |     |     |
| --------------- | ------ | --- | --------------------- | --- | --- | -------- | --------- | ------------ | --- | ------- | -------------- | --- | --- |
|                 |        |     |                       |     |     | internal | variables | that account |     | for the | open-endedness |     |     |
problemsrelatedtodelayedrewardsanditmakesitpossibleto of its development (e.g., cumulative amount of learning
useasimplepredictionsystemwhichcanpredict ,andso progress, evolution of the performance of anticipations,
| evaluate | ,andthenbeusedinastraightforwardac- |     |     |     |     |               |     |            |              |     |            |     |     |
| -------- | ----------------------------------- | --- | --- | --- | --- | ------------- | --- | ---------- | ------------ | --- | ---------- | --- | --- |
|          |                                     |     |     |     |     | and evolution |     | of the way | sensorimotor |     | situations |     | are |
tionselectionloop.Themethodweusetoevaluate categorizedandrepresented).
| givenasensorycontext |     | andacandidateaction |     |     | ,con- |                   |            |            |     |        |          |      |     |
| -------------------- | --- | ------------------- | --- | --- | ----- | ----------------- | ---------- | ---------- | --- | ------ | -------- | ---- | --- |
|                      |     |                     |     |     |       | (cid:127) Second, | behavioral | complexity |     | can be | measured | from | an  |
stituting a candidate sensorimotor context covered by external point of view based on various complexity mea-
region , is straightforward but revealed to be efficient, it is sures (information-theoretical measures such as the ones
equaltothelearningprogressthatwasachievedin withthe presented by Spornsand Pegorscould be used in that re-
acquisitionofitsrecentexemplars,i.e., spect [40]). The increase in behavioral complexity is as-
sessedbypatternchangesinthesemeasures.
|     |     |     |     |     | (3) | (cid:127) Finally,theexperimentercanadoptamethodmoresimilar |     |     |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- | ----------------------------------------------------------- | --- | --- | --- | --- | --- | --- | --- |
tooneusedbyapsychologist,interpretingdevelopmental
| where | isthetimecorrespondingtothelasttimeregion |     |     |     |     |     |     |     |     |     |     |     |     |
| ----- | ----------------------------------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
sequencesasasetofsuccessivestages.Thestagesofde-
andexpert processedanewexemplar. velopment introduced by Piaget are among the most fa-
Based on this predictive mechanism, one can deduce a mousexamplesofsuchqualitativedescriptions[41].Each
straightforwardmechanismwhichmanagesactionselectionin
|     |     |     |     |     |     | transition | between | stages | corresponds |     | to a broad | change |     |
| --- | --- | --- | --- | --- | --- | ---------- | ------- | ------ | ----------- | --- | ---------- | ------ | --- |
ordertomaximizetheexpectedrewardat . in the structure or logic of children’s intelligence and/or
| (cid:127) Inagivensensorycontext |     |     | ,therobotmakesalistofthe |     |     |           |       |             |               |     |            |     |     |
| -------------------------------- | --- | --- | ------------------------ | --- | --- | --------- | ----- | ----------- | ------------- | --- | ---------- | --- | --- |
|                                  |     |     |                          |     |     | behavior. | Based | on clinical | observations, |     | dialogues, |     | and |
possibleactions whichitcando;ifthislistisinfinite, small-scaleexperiments,thepsychologisttriestointerpret
whichisoftenthecasesinceweworkincontinuousaction thesignsofaninternalreorganization.Therefore,theissue
spaces,asampleofcandidateactionsisgenerated. is to map external observations to a series of preexisting
(cid:127) Eachofthesecandidateactions associatedwiththe interpretativemodels.Transitionsaremostofthetimepro-
| context | makes a candidate |     | vector | for which | the |     |     |     |     |     |     |     |     |
| ------- | ----------------- | --- | ------ | --------- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
gressiveandcuttingadevelopmentalsequencesintosharp
robotfindsoutthecorrespondingregion ;thenthefor- divisionisusuallydifficult.
mula we just described is used to evaluate the expected Thefollowingexperimentswillillustratehowacombination
learningprogress thatmightbetheresultof ofsomeofthesemethodscanbeusedtoassessthedevelopment
executingthecandidateaction . ofarobotwithanintrinsicmotivationsystem.

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 273
C. ActionPerceptionLoop
Asaconsequence,themappingthattherobotistryingtolearn
is
|     |     |     |     |     |     | Using       | the IAC   | algorithm, |          | the robot  | will     | thus          | act in | order   |
| --- | --- | --- | --- | --- | --- | ----------- | --------- | ---------- | -------- | ---------- | -------- | ------------- | ------ | ------- |
|     |     |     |     |     |     | to maximize | its       | learning   | progress |            | in terms | of predicting |        | the     |
|     |     |     |     |     |     | next toy    | distance. | The        | robot    | has no     | prior    | knowledge     | and,   | in      |
|     |     |     |     |     |     | particular, | it does   | not        | know     | that there | is       | a qualitative |        | differ- |
Fig.3. Theroboticsetup.Atwo-wheeledrobotmovesinaroomandthere ence between setting the speed of the wheels and setting the
isalsoanintelligenttoy(representedbyasphere)whichmovesaccordingto soundfrequency(fortherobot,theseareunlabeledmotorchan-
thesoundsthattherobotproduces.Therobotperceivesthedistancebetween
nels).Itdoesnotknowthattherearethreezonesofthesensory-
himselfandthetoy.Therobottriestopredictthisdistanceafterperforminga
givenaction,whichisasettingof(leftwheelspeed,rightwheelspeed,sound motor space ofdifferent complexities: the zone corresponding
frequency).Hechoosestheactionsforwhichitpredictsitslearningprogress to sounds in , where the distance to the toy cannot be pre-
willbemaximal.
|     |     |     |     |     |     | dicted since | its | movement | is  | random; | the | zone with | sounds | in  |
| --- | --- | --- | --- | --- | --- | ------------ | --- | -------- | --- | ------- | --- | --------- | ------ | --- |
,wherethedistancetothetoyiseasytolearnandpredict(itis
always0plusanoisecomponentbecauseWebotssimulatesthe
|     | VI. | A FIRST EXPERIMENT |     | WITH | SIMPLE |                                                        |     |     |     |     |     |     |     |     |
| --- | --- | ------------------ | --- | ---- | ------ | ------------------------------------------------------ | --- | --- | --- | --- | --- | --- | --- | --- |
|     |     |                    |     | A    |        | imprecisionofsensorsandactuators);andthezonewithsounds |     |     |     |     |     |     |     |     |
SIMULATEDROBOT in ,wherethedistancetothetoyispredictable(andlearnable)
butcomplexanddependantofthesettingofthewheelspeeds.
| We        | present    | here a robotic | simulation | implemented | with the        |            |          |          |             |        |           |         |                |        |
| --------- | ---------- | -------------- | ---------- | ----------- | --------------- | ---------- | -------- | -------- | ----------- | ------ | --------- | ------- | -------------- | ------ |
|           |            |                |            |             |                 | However,   | we       | will now | show        | that   | the robot | manages |                | to au- |
| Webots    | simulation | software       | [42]. The  | purpose     | of this initial |            |          |          |             |        |           |         |                |        |
|           |            |                |            |             |                 | tonomously | discover |          | these three | zones, | evaluate  |         | their relative |        |
| simulated | experiment | is to show     | and        | understand  | in detail the   |            |          |          |             |        |           |         |                |        |
complexity,andexploitthisinformationfororganizingitsown
workingoftheIACsysteminacontinuoussensorimotorenvi-
behavior.
| ronment | in which | there are parts | which | are | clearly inhomoge- |     |     |     |     |     |     |     |     |     |
| ------- | -------- | --------------- | ----- | --- | ----------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
neousfromthelearningpointofview:thereisapartofthespace
D. Results
whichiseasytolearn,apartofthespacewhichcontainsmore
complexstructureswhichcanbelearned,andapartofthespace First of all, one can study the behavior of the robot during
|     |     |     |     |     |     | a simulation | from | an  | external | point | of view. | A way | to do | that |
| --- | --- | --- | --- | --- | --- | ------------ | ---- | --- | -------- | ----- | -------- | ----- | ----- | ---- |
whichisunlearnable.
|     |     |     |     |     |     | is touse | ourknowledge |     | ofthe | structure | ofthe | environment |     | in  |
| --- | --- | --- | --- | --- | --- | -------- | ------------ | --- | ----- | --------- | ----- | ----------- | --- | --- |
whichtherobotlivesandbuildcorrespondingrelevantmeasures
A. MotorControl
characterizingthebehavioroftherobotwithinagivenperiodof
Therobotisaboxwithtwowheels(seeFig.3).Eachwheel time: 1) the frequency of situations in which it emits a sound
canbecontrolledbysettingitsspeed(realnumberbetween within ; 2) the frequency of situations in which it emits a
and1).Therobotcanalsoemitasoundofaparticularfrequency. sound within ; and 3) the frequency of situations in which
Theactionspaceisthree-dimensionalandcontinuous,andde- itemitsasoundwithin .Fig.4showstheevolutionofthese
ciding for an action consists in setting the values of the motor measuresfor5000timesteps.Severalphasescanbeidentified.
vector (cid:127) Stage 1: Initially, the robot produces all kinds of actions
|     |     |     |     |     |     | with | a uniform |     | probability, | and | in  | particular | produces |     |
| --- | --- | --- | --- | --- | --- | ---- | --------- | --- | ------------ | --- | --- | ---------- | -------- | --- |
soundswithfrequencieswithinthewhole[0,1]spectrum.
|       |                                |     |     |     |               | (cid:127) Stage              | 2:  | After the | first | 250 first | steps,              | the robot | concen- |     |
| ----- | ------------------------------ | --- | --- | --- | ------------- | ---------------------------- | --- | --------- | ----- | --------- | ------------------- | --------- | ------- | --- |
|       |                                |     |     |     |               | tratesonemittingsoundswithin |     |           |       |           | ,andemitssoundswith |           |         |     |
| where | isthespeedofthemotorontheleft, |     |     |     | thespeedofthe |                              |     |           |       |           |                     |           |         |     |
motor on the right, and the frequency of the emitted sound. frequencieswithin or veryrarely.
Therobotmovesinaroom.Thereisatoyinthisroomthatcan (cid:127) Stage3:Thereisthenaphasewithinwhichtherobotcon-
|     |     |     |     |     |     | centratesonemitting |     |     | soundswithin |     |     | ,andemits |     | sounds |
| --- | --- | --- | --- | --- | --- | ------------------- | --- | --- | ------------ | --- | --- | --------- | --- | ------ |
alsomove.Thistoymovesrandomlyifthesoundemittedbythe
robothasafrequencybelongingtozone .Itstops withfrequencieswithin or veryrarely.
movingifthesoundisinzone .Thetoyjumps This shows that the robot consistently avoids the situations
intotherobotifthesoundisinzone . inwhichnothingcanbelearned,beginsbyeasysituations,and
thenshiftsautonomouslytoamorecomplexsituation.
|     |     |     |     |     |     | We can | now | study | what happens |     | from | the robot’s | point | of  |
| --- | --- | --- | --- | --- | --- | ------ | --- | ----- | ------------ | --- | ---- | ----------- | ----- | --- |
B. Perception
view.Fig.5showsarepresentationofthesuccessivevaluesof
The robot perceives the distance to the toy with simulated foralltheregions constructedbytherobotatagiven
infraredsensors,soitssensoryvector isone-dimensional time .Asthe timeisheredefined internallyasthenumberof
actionselectionloops,itcorrespondstothenumberofactions
|     |     |     |     |     |     | that have | been      | chosen | by the   | robot, | and to  | the number |         | of ex- |
| --- | --- | --- | --- | --- | --- | --------- | --------- | ------ | -------- | ------ | ------- | ---------- | ------- | ------ |
|     |     |     |     |     |     | emplars   | that have | been   | provided | to     | it. The | graph      | appears | as a   |
where isthedistancebetweentherobotandthetoyattime . tree,whichcorrespondstothesuccessivesplittingofthespace

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
274 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
Fig.4. Evolutionofthepercentageoftimespentin:1)situationsinwhichtheemittedsoundshaveafrequencywithinf (continuousline);2)situationsinwhich
theemittedsoundshaveafrequencywithinf (dottedline);and3)situationsinwhichtheemittedsoundshaveafrequencywithinf (dashedline).
Bylookingatthetraceofthesimulationandthedefinitions
|     |     |     |     | of the regions                      | associated  |       | to each | curve,      | it is   | possible       | to figure |
| --- | --- | --- | --- | ----------------------------------- | ----------- | ----- | ------- | ----------- | ------- | -------------- | --------- |
|     |     |     |     | out what                            | the regions | which | are     | iteratively | created | look           | like. It  |
|     |     |     |     | appearsthatthefirstsplitappearingat |             |       |         |             |         | correspondstoa |           |
splitbetweensituationsinwhichtherobotemitssoundswitha
|     |     |     |     | frequencywithin                            |             | (      | onthegraph),andsituationsinwhich |      |        |              |           |
| --- | --- | --- | --- | ------------------------------------------ | ----------- | ------ | -------------------------------- | ---- | ------ | ------------ | --------- |
|     |     |     |     | therobotemitssoundswithafrequencywithin    |             |        |                                  |      |        | or           | ( on      |
|     |     |     |     | the graph).                                | To be       | exact, | the system                       | made | a      | split by     | using the |
|     |     |     |     | thirddimensionof                           |             |        | ,i.e.,thefrequency               |      |        | ,andusingthe |           |
|     |     |     |     | cut value                                  | 0.35, which | means  | that                             | the  | region | includes     | pos-      |
|     |     |     |     | siblyasmallportionofsituationswithasoundin |             |        |                                  |      |        |              | ,since    |
beginsat0.34.6Now,weobservethatthecurvecorresponding
to showsasharpdecreaseinitserrorrate,whilethecurve
|     |     |     |     | in shows | an  | increase | in the | error | rate. This | explains | why |
| --- | --- | --- | --- | -------- | --- | -------- | ------ | ----- | ---------- | -------- | --- |
duringthisperiod,therobotwillemitsoundswithfrequencies
|                                           |     |     |                       | within               | : indeed, | this      | corresponds      | to          | situations    | which | are in-     |
| ----------------------------------------- | --- | --- | --------------------- | -------------------- | --------- | --------- | ---------------- | ----------- | ------------- | ----- | ----------- |
|                                           |     |     |                       | ternally evaluatedas |           | providing |                  | the highest | amount        |       | of learning |
| Fig.5. Evolutionofthesuccessivevaluesofhe |     |     | (t)iforalltheregionsR |                      |           |           |                  |             |               |       |             |
|                                           |     |     |                       | progress             | at this   | time of   | its development. |             | Nevertheless, |       | as the      |
constructedbytherobot.
|                                |     |     |              | robotsometimesdoessomerandomactions,theregion |               |                 |     |                         |            |     | ac-         |
| ------------------------------ | --- | --- | ------------ | --------------------------------------------- | ------------- | --------------- | --- | ----------------------- | ---------- | --- | ----------- |
|                                |     |     |              | cumulates                                     | some          | more exemplars, |     | and                     | we observe |     | that around |
| intoregions.Forexample,between |     |     | and ,thereis |                                               |               |                 |     |                         |            |     |             |
|                                |     |     |              |                                               | ,itsplitsinto |                 | and | .Lookingatthetraceshows |            |     |             |
onlyonecurvebecauseduringthattimetherewasonlyonere-
|     |     |     |     | that correspondstosituationswithsoundswithin |     |     |     |     |     |     | and |
| --- | --- | --- | --- | -------------------------------------------- | --- | --- | --- | --- | --- | --- | --- |
gion .Thisinitialcurveisthesequenceofvaluesof . withsoundswithin .Weobservethattheerrorratecontinues
| Then, becausethe | criterion | wasmet,thisregionsplitsinto |     |             |       |           |            |     |     |         |           |
| ---------------- | --------- | --------------------------- | --- | ----------- | ----- | --------- | ---------- | --- | --- | ------- | --------- |
|                  |           |                             |     | to increase | until | a plateau | is reached |     | for | , while | it begins |
two regions and , which also splits the curve into two todecreasefor .Duringthattime,therobotfinallypredicts
| curves, one corresponding |     | to the successive | values of |     |     |     |     |     |     |     |     |
| ------------------------- | --- | ----------------- | --------- | --- | --- | --- | --- | --- | --- | --- | --- |
andtheothercorrespondingtothesuccessivevaluesof . 6ThisalsoshowsthatthesplittingcriteriaC andC thatwepresentedop-
|     |     |     |     | erateefficiently,sincethesystemfindsoutbyitselfthatthisisthef |     |     |     |     |     |     | dimension |
| --- | --- | --- | --- | ------------------------------------------------------------- | --- | --- | --- | --- | --- | --- | --------- |
Then,thecurvessplitagainwhentheirassociatedregionssplit,
whichisthemostrelevantforcuttingthespaceatthebeginningofthedevelop-
| etc. |     |     |     | ment |     |     |     |     |     |     |     |
| ---- | --- | --- | --- | ---- | --- | --- | --- | --- | --- | --- | --- |

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 275
perfectly well situations with sounds with a frequency within
andassociatedwith (itstilltakesawhilebecauseofthe
noise),andaplateaucloseto0intheerrorrateisreached.Thisis
whyatsomepointtherobotshiftstosituationsinwhichitemits
soundswithfrequencieswithin ,whicharesituationswhich
areahighersourceoflearningprogressatthispointinitsdevel-
opment.Therobotthentriestovaryitsmotorspeedswithinthis
subspacewithsoundswithfrequenciesin ,learningtopredict
howthesespeedsaffectthedistancetothetoy.Theaccumula-
tion of new exemplars pushes the robot to split into more
regions,whichisarefinementofitscategorizationofthiskind
ofsituations.Now,thesystemsplitsthespaceusingthe and
dimensions,andtherobotfiguresouthowtoefficientlyexplore
thesubspaceofsituationswithsoundswithfrequencieswithin
,intermsoflearningprogress.
Fig.6. Evolutionoftheperformanceingeneralization(mean-squaredpredic-
E. PerformanceinTermsofActiveLearning tionerror)insituationsinwhichthefrequencyoftheemittedsoundiswithinf
and,respectively,fortheMAXalgorithm(continuousline),theIACalgorithm
Theefficiencyoftheexplorationofthissubspaceofsituations (longdashesline),andtheRANDOMalgorithm(smalldasheslines).Thisal-
lowsustocomparehowmuchtherobothaslearnedoftheinterestingsituations
withsoundsin ,whereinterestingthingscanbelearned,can
afteragivennumberofperformedactions,whenitusesagivenactionselection
beevaluatedifwereformulateIACwithintheproblematicsof algorithm.
activelearning.Thiswillalsoallowustoevaluatetheefficiency
oftheIACalgorithmfromthepointofviewofactivelearning.
Indeed,asweexplainedintheintroduction,inthefieldofma- ForagivensimulationusingagivenalgorithmamongIAC,
chine learning and data mining, the search for methods which MAX,andRANDOM,weevaluateevery100actionsofperfor-
allow us to reduce the number of examples needed to achieve manceingeneralizationofthecurrentlearningmachine.Todo
a given level of performance in generalization for a machine this, we initially made a simulation with random action selec-
which learns an input–output mapping, is of growing interest tionandcollectedadatabaseofinput–outputbystoringtheex-
(here,theinputis andtheoutputis ).While perienced couplesforwhichtheactionin-
IAC was designed as a system for driving the development of cludedanemissionofasoundwithafrequencywithin .This
a robot, it can also be considered as a pure active learning al- provides an independent test set which we used to test the ca-
gorithm,andinthisrespect, itis interestingtoevaluatehowit pacityofpredictionthattherobotacquiredatagiventimeinits
compareswithstandardexistingalgorithms.Thus,wewilluse development.Forthistestwhichisdoneevery100actions,we
two reference algorithms to evaluate the performance of IAC. freezethelearningmachineandmakeitpredicttheoutputcor-
Thefirstonefollowsthemostcommonideainthefieldofac- respondingtoalltheinputsinthetestdatabase.Thefreezingen-
tivelearning[15],[24],[25].Thenextaction(alsocalledquery suresthatthemachinedoesnotlearnwhileitistested.Thepre-
orexperimentdependingontheauthors)ischosensothatitcor- dictionaccuracyismeasuredusingthemean-squarederrorover
respondsaninput–outputpairforwhichthemachineevaluates the database. After evaluating the performance, we unfreezed
thatitspredictionforthispairwillbemaximallyfalseascom- thesystemuntilthenextevaluation.
pared with its prediction for possible other pairs. It is easy to Fig.6showstypicalresultingcurvesofthethreealgorithms.
adaptthisideausingthesamealgorithmicarchitecturethanthe We see that initially, the algorithm which learns fastest is the
oneusedforIAC:whentherobothastodecideforanactionin RANDOMalgorithm.ThisisnormalsinceMAXspendstimes
agivencontext,itmakesthelistofpossibleactionswithinthat inuninterestingsituations,andIACatthebeginningspendstime
context, then for each of them evaluates the expected error in intheeasysituation,soRANDOMisthealgorithmwhichpro-
predictionusingthequantity definedearlier,andfi- vides initially the highest amount of examples related to the
nallychoosestheactionforwhichthisquantityismaximal.Ev- production of the sounds with frequencies within (33% of
erythingelseisequal.Wewillcallthisalgorithm“MAX.”The examples are of this type in this case). Then, after 3000 ac-
secondreferencealgorithmthatweuseisthe“RANDOM”al- tions, the curve corresponding to the IAC algorithm suddenly
gorithm,whichsimplyconsistsinrandomactionselection(and drops down, this corresponds to the shift of attention of the
soisnotanactivelearningalgorithm,butservesasabaseline). robot towards situations with sounds with frequencies within
IAC,MAX,andRANDOMwillbecomparedintermsoftheir . Now, this robot spends 85% of its time in situations with
performanceingeneralizationinpredictingtheconsequenceof soundwithfrequencywithin (andnot100%duetothe0.15
actionsduringwhichasoundwithinthe zoneisemitted.This probabilitytodoarandomaction).Quickly,thecurvegetssig-
meansthatwewillevaluateeachoftheminthepartwhichwe nificantly below the RANDOM algorithm, and reaches a low
knowisinteresting.However,thewholespacewithallrangesof plateau around 5000 actions (where the mean prediction error
frequenciesismadeavailabletotherobot,whichdoesnotknow staysaround0.09).TheRANDOMcurvereachesalowplateau
initiallythatthereisaparticularzonewhereitcanactuallylearn much later (this is not represented on this curve) after about
nontrivialthings. 11000actions.Thevalueoftheplateau,interestingly,ishigher

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
276 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
thanwiththeIACalgorithm,itis0.096.Werepeatedtheexperi-
ments100timesinordertoseewhetherthishadsomestatistical
| significance. | In  | each simulation, |     | we measured |     | the time | where |     |     |     |     |     |     |     |     |
| ------------- | --- | ---------------- | --- | ----------- | --- | -------- | ----- | --- | --- | --- | --- | --- | --- | --- | --- |
aplateauwasreached(definedas500successivepoints,where
themean-squarederrorhasavariancesmallerthan0.0001),and
whatthemean-squarederrorwasatthattime.Itturnedoutthat
| the plateau                   | was | reachedat |     |     | in averagefor |               | IAC, with |     |     |     |     |     |     |     |     |
| ----------------------------- | --- | --------- | --- | --- | ------------- | ------------- | --------- | --- | --- | --- | --- | --- | --- | --- | --- |
| astandarddeviationof452,andat |     |           |     |     |               | inaveragewith |           |     |     |     |     |     |     |     |     |
astandarddeviationof561forRANDOM.Themean-squared
| errorwas      |     | inaveragewithastandarddeviationof0.009 |                               |     |     |     |     |     |     |     |     |     |     |     |     |
| ------------- | --- | -------------------------------------- | ----------------------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| forIAC,andwas |     |                                        | withastandarddeviationof0.004 |     |     |     |     |     |     |     |     |     |     |     |     |
forRANDOM.Asaconsequence,wecansayconsistentlythat
| IAC allows     | the     | robot to     | learn  | the interesting |          | part of     | the map- |     |     |     |     |     |     |     |     |
| -------------- | ------- | ------------ | ------ | --------------- | -------- | ----------- | -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| ping about     | 2.6     | times faster | and    | with            | a higher | performance |          | in  |     |     |     |     |     |     |     |
| generalization | thanthe |              | RANDOM | algorithm.This  |          | increaseof  |          |     |     |     |     |     |     |     |     |
theperformancesingeneralizationissimilartowhathasalready
beendescribedinotheractivelearningalgorithms[32].
F. Summary
Withthisexperiment,wehaveshownafirstembodimentof Fig.7. Theplaygroundexperimentsetup.
| the IAC | system  | within | a simulated | robot.          | This | has allowed | us        |     |     |     |     |     |     |     |     |
| ------- | ------- | ------ | ----------- | --------------- | ---- | ----------- | --------- | --- | --- | --- | --- | --- | --- | --- | --- |
| to show | how IAC | could  | manage      | the development |      | of          | the robot |     |     |     |     |     |     |     |     |
in an inhomogeneous sensorimotor environment with parts which two- or three-month old children learn their first sen-
|     |     |     |     |     |     |     |     | sorimotor | skills, | although | the | sensorimotor | apparatus |     | of the |
| --- | --- | --- | --- | --- | --- | --- | --- | --------- | ------- | -------- | --- | ------------ | --------- | --- | ------ |
whichwerenotlearnablebytherobot.Wehaveshownhowthe
robot consistently avoided this zone of unlearnability and, on robothereismuchmorelimited.Wehavedevelopedawebsite
theotherhand,exploredautonomouslysensorimotorsituations which presents pictures and videos of this setup: http://play-
ofincreasingcomplexity.Thissimplesetupalsoallowedusto ground.csl.sony.fr/.
| detail the | evolution | of  | the internal | structures |     | built by | the IAC |     |     |     |     |     |     |     |     |
| ---------- | --------- | --- | ------------ | ---------- | --- | -------- | ------- | --- | --- | --- | --- | --- | --- | --- | --- |
system.Wecouldexplain,forexample,theprogressiveforma- A. MotorControl
| tion of | regions | with varying | potentials |     | for learning |     | progress. |     |     |     |     |     |     |     |     |
| ------- | ------- | ------------ | ---------- | --- | ------------ | --- | --------- | --- | --- | --- | --- | --- | --- | --- | --- |
Therobotisequippedinitiallyonlywithsimplemotorprimi-
| Finally, | this setup | not only | allowed | us  | to show | the | interest of |     |     |     |     |     |     |     |     |
| -------- | ---------- | -------- | ------- | --- | ------- | --- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
tives.Inparticular,itisnotabletowalkaround.Therearethree
| IAC as   | an intrinsic | motivation |     | system               | which | could | self-orga- |             |             |         |     |           |          |     |        |
| -------- | ------------ | ---------- | --- | -------------------- | ----- | ----- | ---------- | ----------- | ----------- | ------- | --- | --------- | -------- | --- | ------ |
|          |              |            |     |                      |       |       |            | basic motor | primitives: | turning |     | the head, | bashing, | and | crouch |
| nize the | behaviorof   | a robot    | ina | developmentalmanner, |       |       | butit      |             |             |         |     |           |          |     |        |
biting.Eachofthemiscontrolledbyanumberofrealnumber
alsoshowedthatIACisanefficientandrobustactivelearning
parameters,whicharetheactionparametersthattherobotcon-
| system. | Indeed,   | we proved | that        | it was | faster          | than | both the |            |          |       |           |               |     |      |         |
| ------- | --------- | --------- | ----------- | ------ | --------------- | ---- | -------- | ---------- | -------- | ----- | --------- | ------------- | --- | ---- | ------- |
|         |           |           |             |        |                 |      |          | trols. The | “turning | head” | primitive | is controlled |     | with | the pan |
| RANDOM  | algorithm | and       | traditional |        | active learning |      | methods  |            |          |       |           |               |     |      |         |
andtiltparametersoftherobot’shead.The“bashing”primitive
whicharenotsuitedtomappingswithstronginhomogeneities
|     |     |     |     |     |     |     |     | is controlled | with | the strength | and | the angle | of  | the leg | move- |
| --- | --- | --- | --- | --- | --- | --- | --- | ------------- | ---- | ------------ | --- | --------- | --- | ------- | ----- |
andevenunlearnableparts.
ment(alowerlevelautomaticmechanismtakescareofsetting
However,thesimplicityofthissetupdidnotallowustoshow
theindividualmotorscontrollingtheleg).The“crouchbiting”
| how a developmental |     | sequence |     | with | more than | one | transition |     |     |     |     |     |     |     |     |
| ------------------- | --- | -------- | --- | ---- | --------- | --- | ---------- | --- | --- | --- | --- | --- | --- | --- | --- |
primitiveiscontrolledbythedepthofcrouching(andtherobot
couldself-organizeautonomously(here,therewasonlyatran-
crouchesinthedirectioninwhichitislookingat,whichisdeter-
sition between a stage in which the robot focused on actions minedbythepanandtiltparameters).Tosummarize,choosing
| withsoundsin |      | ,andthenastageinwhichtherobotfocused |       |        |           |     |         |           |          |            |     |            |     |                 |     |
| ------------ | ---- | ------------------------------------ | ----- | ------ | --------- | --- | ------- | --------- | -------- | ---------- | --- | ---------- | --- | --------------- | --- |
|              |      |                                      |       |        |           |     |         | an action | consists | in setting | the | parameters | of  | the five-dimen- |     |
| on actions   | with | sounds                               | in ). | We are | now going | to  | present | a         |          |            |     |            |     |                 |     |
sionalcontinuousvector
morecomplexexperimentinwhichwewillshowthatmultiple
sequentiallevelsofself-organizationofthebehavioroftherobot
can happen.
|     |     |     |     |     |     |     |     | where        | is the | pan of      | the head,  | the | tilt of | the head, |        |
| --- | --- | --- | --- | --- | --- | --- | --- | ------------ | ------ | ----------- | ---------- | --- | ------- | --------- | ------ |
|     |     |     |     |     |     |     |     | the strength | of     | the bashing | primitive, |     | the     | angle     | of the |
VII. THEPLAYGROUNDEXPERIMENT:THEDISCOVERYOF
|     |     |     |     |     |     |     |     | bashing | primitive, | and | the depth | of  | the crouching |     | of the |
| --- | --- | --- | --- | --- | --- | --- | --- | ------- | ---------- | --- | --------- | --- | ------------- | --- | ------ |
SENSORIMOTORAFFORDANCES
|     |     |     |     |     |     |     |     | robot for | the | biting motor | primitive. |     | All | values are | real |
| --- | --- | --- | --- | --- | --- | --- | --- | --------- | --- | ------------ | ---------- | --- | --- | ---------- | ---- |
This new experimental setup is called “The Playground numbers between 0 and 1, plus the value which is a con-
Experiment.”Thisinvolvesaphysicalrobotaswellasamore vention used for not using a motor primitive, for example,
complexsensorimotorsystemandenvironment.WeuseaSony correspondstothecombina-
AIBO robot which is put on a baby play mat with various tionofturningtheheadwithparameters and
toys that can be bitten, bashed, or simply visually detected withthebitingprimitivewiththeparameter butwith
(see Fig. 7). The environment is very similar to the ones in nobashingmovement.

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 277
B. Perception for the IAC system is implemented. On the one hand, this al-
Therobotisequippedwiththreehigh-levelsensorsbasedon lows the robot to make all the measures necessary for deter-
|             |          |     |                |     |         |           | mining | adequate | values | of  |     | . On | the | other hand, |
| ----------- | -------- | --- | -------------- | --- | ------- | --------- | ------ | -------- | ------ | --- | --- | ---- | --- | ----------- |
| lower level | sensors. | The | sensory vector |     | is thus | three-di- |        |          |        |     |     |      |     |             |
mensional andmostimportantly,thisallowstheenvironmenttocomeback
|     |     |     |     |     |     |     | to its “resting | state.” |     | This means | that | the environment |     | has no |
| --- | --- | --- | --- | --- | --- | --- | --------------- | ------- | --- | ---------- | ---- | --------------- | --- | ------ |
memory:afteranactionhasbeenexecutedbytherobot,allthe
|     |     |     |     |     |     |     | objects | are back | in the | same | state. For | example, | if  | the object |
| --- | --- | --- | --- | --- | --- | --- | ------- | -------- | ------ | ---- | ---------- | -------- | --- | ---------- |
where:
thatcanbebashedhasactuallybeenbashed,thenithasstopped
(cid:127) isthebinaryvalueofanobjectvisualdetectionsensor:
|          |     |         |          |            |            |     | oscillating | before | the | robots | tries a new | action. | This | is a de- |
| -------- | --- | ------- | -------- | ---------- | ---------- | --- | ----------- | ------ | --- | ------ | ----------- | ------- | ---- | -------- |
| It takes | the | value 1 | when the | robot sees | an object, | and | 0           |        |     |        |             |         |      |          |
liberatechoicetohaveanenvironmentwithnomemory,while
intheothercase.Intheplayground,weusesimplevisual
|     |     |     |     |     |     |     | keeping | all the | advantages, | the | constraints | and | the | complexity |
| --- | --- | --- | --- | --- | --- | --- | ------- | ------- | ----------- | --- | ----------- | --- | --- | ---------- |
tagsthatwestickonthetoysandareeasytodetectfromthe
ofaphysicalembodiment,thismakesthemappingfromactions
imageprocessingpointofview.Thesetagsareblackand
|     |     |     |     |     |     |     | to perception | learnable |     | in a reasonable |     | time. | This | is crucial if |
| --- | --- | --- | --- | --- | --- | --- | ------------- | --------- | --- | --------------- | --- | ----- | ---- | ------------- |
whitepatternssimilartotheCybercodesystemdeveloped
onewantstodoseveralexperiments(alreadyinthiscase,each
byRekimoto([43]).
experimentlastsfornearlyoneday).Furthermore,introducing
(cid:127) isthebinaryvalueofabitingsensor:Ittakesthevalue1
|     |     |     |     |     |     |     | an environment |     | withmemory |     | frames | the problem | of  | the max- |
| --- | --- | --- | --- | --- | --- | --- | -------------- | --- | ---------- | --- | ------ | ----------- | --- | -------- |
whentherobothassomethinginitsmouthand0otherwise.
|     |     |     |     |     |     |     | imization | of internal | reward |     | within delayed |     | reward | reinforce- |
| --- | --- | --- | --- | --- | --- | --- | --------- | ----------- | ------ | --- | -------------- | --- | ------ | ---------- |
WeusethecheeksensoroftheAIBO.
mentproblems,forwhichthereexistspowerfulbutcomplicated
(cid:127) isthebinaryvalueofanoscillationsensor:Ittakesthe
techniqueswhosebiaseswouldcertainlymaketheresultsmore
value1whentherobotdetectsthatthereissomethingos-
complexandrenderthemmoredifficulttointerpret.
cillatinginfrontofit,and0,otherwise.Weusetheinfrared
| distancesensoroftheAIBOtoimplementthishigh-level |     |     |     |     |     |     | D. Results |     |     |     |     |     |     |     |
| ------------------------------------------------ | --- | --- | --- | --- | --- | --- | ---------- | --- | --- | --- | --- | --- | --- | --- |
sensor.Thissensorcandetect,forexample,whenthereis
Duringanexperiment,wecontinuouslymeasureanumberof
anobjectthathasbeenbashedinthedirectionoftherobot’s
featureswhichhelpuscharacterizethedynamicsoftherobot’s
| gaze,  | but can | also       | detect events | due    | to human | walking      |              |        |     |         |               |     |        |           |
| ------ | ------- | ---------- | ------------- | ------ | -------- | ------------ | ------------ | ------ | --- | ------- | ------------- | --- | ------ | --------- |
|        |         |            |               |        |          |              | development. | First, | we  | measure | the frequency |     | of the | different |
| around | the     | playground | (we           | do not | control  | the environ- |              |        |     |         |               |     |        |           |
kindsofactionsthattherobotperformsinagiventimewindow.
ment).
Moreprecisely:
Itiscrucialtonotethatinitiallytherobotknowsnothingabout
|     |     |     |     |     |     |     | (cid:127) the | percentage | of  | actions | which | do not | involvethe | biting |
| --- | --- | --- | --- | --- | --- | --- | ------------- | ---------- | --- | ------- | ----- | ------ | ---------- | ------ |
sensorimotoraffordances.Forexample,itdoesnotknowthatthe
andthebashingmotorprimitiveinthelast100actions(i.e.,
valuesoftheobjectvisualdetectionsensorarecorrelatedwith
|            |        |         |               |     |           |            | therobot’saction |     |     | boilsdownto“justlooking”inagiven |     |     |     |     |
| ---------- | ------ | ------- | ------------- | --- | --------- | ---------- | ---------------- | --- | --- | -------------------------------- | --- | --- | --- | --- |
| the values | of its | pan and | tilt. It does | not | know that | the values |                  |     |     |                                  |     |     |     |     |
direction);
| of the biting | or  | object oscillation |     | sensors | can become | 1 only |               |            |     |         |       |         |            |       |
| ------------- | --- | ------------------ | --- | ------- | ---------- | ------ | ------------- | ---------- | --- | ------- | ----- | ------- | ---------- | ----- |
|               |     |                    |     |         |            |        | (cid:127) the | percentage | of  | actions | which | involve | the biting | motor |
whenbitingorbashingactionsareperformedtowardsanobject.
primitiveinthelast100actions;
Itdoesnotknowthatsomeobjectsaremorepronetoprovoke
(cid:127) thepercentageofactionwhichinvolvethebashingmotor
| changesinthevaluesofthe |     |     | and | sensorswhenonlycer- |     |     |     |     |     |     |     |     |     |     |
| ----------------------- | --- | --- | --- | ------------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
primitiveinthelast100action.
tainkindsofactionsareperformedintheirdirection.Itdoesnot
Then,wetrackthegazeoftherobotandateachaction,mea-
know,forexample,thattogetachangeinthevalueoftheos-
sureifitislookingtowards:1)thebitableobject,or2)thebash-
cillationsensor,bashinginthecorrectdirectionisnotenough,
|     |     |     |     |     |     |     | able object, | or  | 3) no object. | This | is possible |     | from | an external |
| --- | --- | --- | --- | --- | --- | --- | ------------ | --- | ------------- | ---- | ----------- | --- | ---- | ----------- |
becauseitalsoneedstolookintherightdirection(sinceitsos-
pointofviewsinceweknowwheretheobjectsareandsoitis
| cillation | sensors | are on | the front | of its head). | These | remarks |     |     |     |     |     |     |     |     |
| --------- | ------- | ------ | --------- | ------------- | ----- | ------- | --- | --- | --- | --- | --- | --- | --- | --- |
easytoderivetheinformationfromtheheadposition.
allowustounderstandeasilythatarandomstrategywillnotbe
|     |     |     |     |     |     |     | Third, | we measure |     | the evolution |     | of the frequency |     | of suc- |
| --- | --- | --- | --- | --- | --- | --- | ------ | ---------- | --- | ------------- | --- | ---------------- | --- | ------- |
efficientinthisenvironment.Iftherobotwoulddorandomac-
|     |     |     |     |     |     |     | cessful | biting actions |     | and the | evolution | of successful |     | bashing |
| --- | --- | --- | --- | --- | --- | --- | ------- | -------------- | --- | ------- | --------- | ------------- | --- | ------- |
tionselection,inavastmajorityofcases,nothingwouldhappen
actions.Asuccessfulbitingactionisdefinedasanactionwhich
| (especiallyforthe |     | and | sensors). |     |     |     |          |       |       |        |        |            |     |          |
| ----------------- | --- | --- | --------- | --- | --- | --- | -------- | ----- | ----- | ------ | ------ | ---------- | --- | -------- |
|                   |     |     |           |     |     |     | provokes | a “1” | value | on the | sensor | (an object | has | actually |
C. TheActionPerceptionLoop be bitten). A successful bashing action is defined as an action
|     |     |     |     |     |     |     | whichprovokesanoscillationinthe |     |     |     |     | sensor. |     |     |
| --- | --- | --- | --- | --- | --- | --- | ------------------------------- | --- | --- | --- | --- | ------- | --- | --- |
Tosummarize,themappingthattherobothastolearnis
Fig.8showsanexampleoftheresultsoftheevolutionofthe
threekindsofmeasuresonthreedifferentlevels.Astrikingfea-
tureofthesecurvesistheformationofsequencesofpeaks.Each
ofthesepeaksbasicallymeansthatatthemomentitoccursthe
robotisfocusingitsactivityanditsattentiononasmallsubset
The robot is equipped with the IAC system, and thus chooses ofthesensorimotorspace.Therefore,itisqualitativelydifferent
its actions according to the potential learning progress that it fromrandomactionperformanceinwhichthecurveswouldbe
can provide to one of its experts. In this experiment, the ac- stationaryandratherflat.Bylookingindetailatthesepeaksand
tionperceptionloopisratherlong.Whentherobotchoosesand attheirco-occurence(ornot)withinthedifferentkindsofmea-
executes an action, it waits until all its motor primitives have sures,wecanmakeadescriptionoftheevolutionoftherobot’s
finishedtheirexecution,whichlastsapproximatelyonesecond, behavior. In Fig. 8, we have marked a number of such peaks
beforechoosingthenextaction.Thisishowtheinternalclock withlettersfromAtoG.Wecanseethatbeforethefirstpeak,

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
278 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
Fig.8. Curvesdescribingarunoftheplaygroundexperiment.Top3:Frequenciesforcertainactiontypesonwindows100timestepswide.Mid3:Frequenciesof
gazedirectiontowardscertainobjectsinwindows200timestepswide:“object1”referstothebitableobject,and“object2”referstothebashableobject.Bottom
3:Frequenciesofsuccessfulbiteandsuccessfulbashinwindows200timestepswide.
there is an initial phase during which all actions are produced Then,thereispeak(E)correspondingtofocusingonthebiting
equallyoften,thatmostoftennoobjectisseen,andthatasuc- action,whichisthistimecoupledwithapeakinthecurvemon-
cessfulbiteorbashonlyhappensextremelyrarely.Thiscorre- itoring the looking direction towards the bitable object, and a
spondstoaphaseofrandomactionselection.Indeed,therobot peakinthecurvemonitoringthesuccessinbiting.Itmeansthat
initiallycategorizesthesensorimotorspaceusingonlyonebig duringthisperiod,the robotusesthe actionprimitivewiththe
region(andsothereisonlyonecategory),andsoallactionsin right affordances, and manages to bite the bitable object quite
any contexts are equally interesting. Then, we observe a peak often.Thispeakisthenrepeatedalittlebitlater(F).Finally,a
(A)inthe“justlooking”curve.Thismeansthatforawhile,the co-occurrenceofpeaks(G)appearsthatcorrespondstoaperiod
robot stopsbiting and bashing,and focusesonjust movingits duringwhichtherobotconcentratesonusingthebashingprim-
headaround.Thismeansthatatthispointtherobothassplitthe itivewith the right affordances, managing to actually bash the
spaceintoseveralregions,andthataregioncorrespondingtothe bashableobjectquiteoften.
sensorimotorloopof“justlookingaround”isassociatedtothe Thisexampleshowsthatseveralinterestingphenomenahave
highestlearningprogressfromtherobot’spointofview.Then, appearedinthisrunoftheexperiment.Firstofall,thepresence
the next peak (B) corresponds to a focus on the biting action andco-occurrenceofpeaksofvariouskindsshowsaself-organ-
primitive(withvariouscontinuousparameters),butitdoesnot izationofthebehavioroftherobot,whichfocusesonparticular
co-occur with looking towards the bitable object. This means sensorimotor loops at different periods in time. Second, when
thattherobotisbasicallytryingtobiteinalldirectionsaroundit, weobservethesepeaks,weseethattheyarenotrandompeaks,
itdidnotdiscoveryettheaffordancesofthebitingactionswith but show a progressive increase in the complexity of the be-
particularobjects.Thenextpeak(C)correspondstoafocuson haviortowhichtheycorrespond.Indeed,onehastoremember
the bashingaction primitive(withvarious continuous parame- thattheintrinsicdimensionalityofthe“justlooking”behavior
ters),butagaintherobotdoesnotlooktowardsaparticulardi- (pan and tilt) is lower than the one of the “biting” behavior
rection. As the only way to discover that a bashing action can (whichaddsthedepthofthecrouchingmovement),whichisin
make anobjectmoveisbylookinginthedirectionofthisob- itselflowerthantheoneofthe“bashing”behavior(whichadds
ject(becausetheIRsensorisonthecheek),thismeansthatthe theangleandthestrengthdimensions).Theorderofappearance
robot does not use at this point the bashing primitive with the oftheperiodswithinwhichtherobotfocusesononeoftheseac-
right affordances. The next peak (D) corresponds to a period tivitiesispreciselythesame.Ifwelookinmoredetail,wealso
withinwhichtherobotagainstopsbitingandbashingandcon- seethatthebitingbehaviorappearsfirstinanonaffordantver-
centratesonmovingthehead,butthistimeweobservethatthe sion(therobottriestobitethingswhichcannotbebitten),and
robot focuses these “looking” movements in a narrow part of thenonlylaterintheaffordantversion(whereittriestobitethe
the visual field, it is basically looking around one of the ob- biteableobject).Thesameobservationholdsforthebashingbe-
jects, learning howit disappears/reappearsin its field of view. havior: first, it appears without the right affordances, and then

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 279
itappearswiththerightaffordances.Theformationoffocused
|     |     |     |     |     |     |     |     |     |     |     | TABLE | I   |     |     |     |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ----- | --- | --- | --- | --- |
activitieswhosepropertiesevolveandarerefinedwithtimecan STATISTICAL MEASURES ON THE 200 SIMULATION-BASED EXPERIMENTS
beusedtodescribethedevelopmentaltrajectoriesthataregen-
eratedintermsofstages.Indeed,onecandefinethatanewstage
beginswhenaco-occurenceofpeaksthatneveroccurredbefore
happens(andwhichdenotesanovelkindoffocusedactivity).
Werantheexperimentseveraltimeswiththerealrobots,and
| whereas | each | particular | experiment |     | produced | curves | which |     |     |     |     |     |     |     |     |
| ------- | ---- | ---------- | ---------- | --- | -------- | ------ | ----- | --- | --- | --- | --- | --- | --- | --- | --- |
weredifferentinthedetails,itseemedthatsomeregularitiesin
thepatternsofpeakformation,andintermsofstagesequences
werepresent.Wethenproceededtomoreexperimentsinorderto
preciselyassessthestatisticalpropertiesoftheseself-organized
| developmental |           | trajectories. | As        | each     | experiment    | with             | the real   |                    |     |              |     |      |                  |     |        |
| ------------- | --------- | ------------- | --------- | -------- | ------------- | ---------------- | ---------- | ------------------ | --- | ------------ | --- | ---- | ---------------- | --- | ------ |
| robot lasts   | several   | hours,        | and       | in order | to be         | able to          | run many   |                    |     |              |     |      |                  |     |        |
| experiments   | (200),    | we            | developed | a        | model of      | the experimental |            |                    |     |              |     |      |                  |     |        |
| setup. Thanks |           | to the        | fact that | the      | physical      | environment      | was        |                    |     |              |     |      |                  |     |        |
| memoryless    | after     | each          | action    | of the   | robot,        | it was           | possible   | to                 |     |              |     |      |                  |     |        |
| make an       | accurate  | model         | of        | it using | the following |                  | procedure. |                    |     |              |     |      |                  |     |        |
|               |           |               |           |          |               |                  |            | (cid:127) (Measure | 4)  | Nonaffordant |     | bite | before affordant |     | bite?: |
| We let        | the robot | perform       |           | several  | thousand      | actions          | and we     |                    |     |              |     |      |                  |     |        |
Isthereisapeakcorrespondingto“biting”withthewrong
| recorded   | each         | time |            | and   |            | . Then, | from this |             |       |     |         |              |        |               |     |
| ---------- | ------------ | ---- | ---------- | ----- | ---------- | ------- | --------- | ----------- | ----- | --- | ------- | ------------ | ------ | ------------- | --- |
|            |              |      |            |       |            |         |           | affordances | which |     | appears | before       | a peak | corresponding |     |
| database   | of examples, |      | we trained | a     | prediction | machine | based     |             |       |     |         |              |        |               |     |
|            |              |      |            |       |            |         |           | to “biting” | with  | the | right   | affordances? |        |               |     |
| on locally | weighted     |      | regression | [44]. | This       | machine | was then  |             |       |     |         |              |        |               |     |
(cid:127) (Measure5)Nonaffordantbashbeforeaffordantbash?:
| used as | a model | of  | the physical |     | environment | and | the IAC |     |     |     |     |     |     |     |     |
| ------- | ------- | --- | ------------ | --- | ----------- | --- | ------- | --- | --- | --- | --- | --- | --- | --- | --- |
Isthereapeakcorrespondingto“bashing”withthewrong
| algorithm | of             | the robot | was           | directly | plugged | into             | it.      |              |       |      |           |              |        |               |     |
| --------- | -------------- | --------- | ------------- | -------- | ------- | ---------------- | -------- | ------------ | ----- | ---- | --------- | ------------ | ------ | ------------- | --- |
|           |                |           |               |          |         |                  |          | affordances  | which |      | appears   | before       | a peak | corresponding |     |
| Using     | this simulated |           | world         | setup,   | we ran  | 200 experiments, |          |              |       |      |           |              |        |               |     |
|           |                |           |               |          |         |                  |          | to “bashing” |       | with | the right | affordances? |        |               |     |
| each time | monitoring     |           | the evolution |          | using   | the same         | measures |              |       |      |           |              |        |               |     |
(cid:127) (Measure6)Periodofsystematicsuccessfulbite?:Does
| as above.  | We      | then constructed |       | higher      | level  | measures | on each    |           |            |                |         |                 |           |          |         |
| ---------- | ------- | ---------------- | ----- | ----------- | ------ | -------- | ---------- | --------- | ---------- | -------------- | ------- | --------------- | --------- | -------- | ------- |
|            |         |                  |       |             |        |          |            | the robot | succeed    | systematically |         |                 | in biting | often at | some    |
| of the     | runs,   | based on         | the   | structure   | of the | peak     | sequence.  |           |            |                |         |                 |           |          |         |
|            |         |                  |       |             |        |          |            | point     | ( is there | a              | peak in | the “successful |           | bite”    | curve)? |
| Peaks were | defined | here             | using | a threshold |        | on the   | height and |           |            |                |         |                 |           |          |         |
(cid:127) (Measure7)Periodofsystematicsuccessfulbash?:Does
| width of | the bumps | in  | the curves. | These | measures |     | correspond |     |     |     |     |     |     |     |     |
| -------- | --------- | --- | ----------- | ----- | -------- | --- | ---------- | --- | --- | --- | --- | --- | --- | --- | --- |
therobotsucceedsystematicallyinbashingoftenatsome
| to the             | answers | to these  | following |            | questions. |      |           |       |            |     |         |                 |     |       |        |
| ------------------ | ------- | --------- | --------- | ---------- | ---------- | ---- | --------- | ----- | ---------- | --- | ------- | --------------- | --- | ----- | ------ |
|                    |         |           |           |            |            |      |           | point | ( is there | a   | peak in | the “successful |     | bash” | curve? |
| (cid:127) (Measure |         | 1) Number |           | of peaks?: | How        | many | peaks are |       |            |     |         |                 |     |       |        |
(cid:127) (Measure8)Bitebeforebash?:Isthereafocusonbiting
| there | in  | the action | curves | (top | curves)? |     |     |       |         |        |         |     |                        |     |     |
| ----- | --- | ---------- | ------ | ---- | -------- | --- | --- | ----- | ------- | ------ | ------- | --- | ---------------------- | --- | --- |
|       |     |            |        |      |          |     |     | which | appears | before | a focus | on  | bashing (independantly |     |     |
(cid:127) (Measure2)Completescenario?:Isthefollowingdevel-
of affordance)?
opmentalscenariomatched:first,thereisa“justlooking”
|            |       |             |           |               |       |              |           | (cid:127) (Measure | 9) Successful   |                 | bite     | before   | successful     | bash?: | Is     |
| ---------- | ----- | ----------- | --------- | ------------- | ----- | ------------ | --------- | ------------------ | --------------- | --------------- | -------- | -------- | -------------- | ------ | ------ |
| peak,      | then  | there       | is a peak | corresponding |       | to “biting”  | with      |                    |                 |                 |          |          |                |        |        |
|            |       |             |           |               |       |              |           | there              | a focus         | on successfully |          | biting   | which          | appear | before |
| the        | wrong | affordances |           | which appears |       | before a     | peak cor- |                    |                 |                 |          |          |                |        |        |
|            |       |             |           |               |       |              |           | a focus            | on successfully |                 |          | bashing? |                |        |        |
| responding |       | to “biting” |           | with the      | right | affordances, | and       |                    |                 |                 |          |          |                |        |        |
|            |       |             |           |               |       |              |           | The numerical      | results         |                 | of these | measures | are summarized |        | in     |
thereisapeakcorrespondingto“bashing”withthewrong
TableI.Thistableshowsthatindeedsomestructuralandstatis-
| affordances |           | which | appears   | before      | a peak | corresponding |          |                    |       |     |                    |     |               |     |      |
| ----------- | --------- | ----- | --------- | ----------- | ------ | ------------- | -------- | ------------------ | ----- | --- | ------------------ | --- | ------------- | --- | ---- |
|             |           |       |           |             |        |               |          | tical regularities | arise | in  | the self-organized |     | developmental |     | tra- |
| to          | “bashing” | with  | the right | affordances |        | (and the      | relative |                    |       |     |                    |     |               |     |      |
jectories.Firstofall,onehastonotethatthecomplexandstruc-
| order | between | “biting”-related |     |     | peaks | and “bashing”-re- |     |     |     |     |     |     |     |     |     |
| ----- | ------- | ---------------- | --- | --- | ----- | ----------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
turedtrajectorydescribedbyMeasure2appearsin34%ofthe
| lated | peaks | is ignored). |     | Biting | with the | right | affordance |     |     |     |     |     |     |     |     |
| ----- | ----- | ------------ | --- | ------ | -------- | ----- | ---------- | --- | --- | --- | --- | --- | --- | --- | --- |
cases,whichishighgiventhenumberofpossibleco-occurences
| is  | defined | here | as the | co-occurence |     | between | a peak | in             |        |     |               |     |            |               |     |
| --- | ------- | ---- | ------ | ------------ | --- | ------- | ------ | -------------- | ------ | --- | ------------- | --- | ---------- | ------------- | --- |
|     |         |      |        |              |     |         |        | of peaks which | define | a   | combinatorics |     | of various | trajectories. |     |
the “biting” curve and a peak in the “seeing the biteable Furthermore, if we remove the test on “just looking,” we see
| object” | curve, | and | biting | with | the wrong | affordances |     | is          |          |                 |     |       |      |            |     |
| ------- | ------ | --- | ------ | ---- | --------- | ----------- | --- | ----------- | -------- | --------------- | --- | ----- | ---- | ---------- | --- |
|         |        |     |        |      |           |             |     | that in the | majority | of experiments, |     | there | is a | systematic | se- |
defined as all other situations. The corresponding defi- quencingfromnonaffordanttoaffordantactionsforbothbiting
| nition | applies | to  | “bashing.” |     |     |     |     |     |     |     |     |     |     |     |     |
| ------ | ------- | --- | ---------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
andbashing.Thisshowsanorganizedandprogressiveincrease
(cid:127) (Measure3)Nearlycompletescenario?:Isthefollowing
|     |     |     |     |     |     |     |     | in the complexity |     | of the | behavior. | Another | measure | confirms |     |
| --- | --- | --- | --- | --- | --- | --- | --- | ----------------- | --- | ------ | --------- | ------- | ------- | -------- | --- |
lessconstraineddevelopmentalscenariomatched.Thereis this increase of complexity from another point of view: if we
apeakcorrespondingto“biting”withthewrongaffordances
comparetherelativeorderofappearanceofperiodsoffocused
whichappearsbeforeapeakcorrespondingto“biting”with biteorbash,thenwefindthat“focusedbite”appearsinthelarge
| the | right | affordances, | and | there | is a peak | corresponding |     |             |           |        |     |          |        |       |        |
| --- | ----- | ------------ | --- | ----- | --------- | ------------- | --- | ----------- | --------- | ------ | --- | -------- | ------ | ----- | ------ |
|     |       |              |     |       |           |               |     | majority of | the cases | before | the | “focused | bash,” | which | corre- |
to “bashing” with the wrong affordances which appears sponds to their relative intrinsic dimension (3 for biting and 4
before a peak corresponding to “bashing” with the right forbashing).Finally,onecannotethattherobotin100%ofthe
affordances(andtherelativeorderbetween“biting”-related
experiments,reachesaperiodduringwhichitrepeatedlyman-
peaks and “bashing”-related peaks is ignored). agestobitethebiteableobject,andin78%oftheexperiments,

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
280 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
Fig.9. Variousrunsofthesimulatedexperiments.Inthetopsquares,weobservetwotypicaldevelopmentaltrajectoriescorrespondingtothe“completescenario”
describedbyMeasure1.Inthebottomcurve,weobserverarebutexistingdevelopmentaltrajectories.
itreachesaperiodduringwhichitrepeatedlymanagestobash fromindividualtoindividualtherecanbesomesubstantialdif-
thebashableobject.Thislastpointisinterestingsincetherobot ferences(e.g.,someinfantslearnhowtocrawlbeforetheycan
wasnotpreprogrammedtoachievethisparticulartask. sitandothersdothereverse).
Theseexperimentsshowhowtheintrinsicmotivationsystem
which is implemented (IAC) drives the robot into a self-or- VIII. DISCUSSION
ganized developmental trajectory in which periods of focused
A. DevelopingComplexBehavioralSchemas
sensorimotor activities of progressively increasing complexity
arise.Wehaveseenthatanumberofstructuralregularitiesarose Wehavediscussedhowtodesignasystemofinternalrewards
in the system, such as the tendancy of nonaffordant behavior suitedforactiveandautonomousdevelopment.Suchanintrinsic
to be explored before affordant behavior, or the tendancy to motivation system permits us to realize an efficient active ex-
explore a certain kind of behavior (bite) before another kind plorationofagivensensorimotorspace.Intheexperimentsde-
(bash).Yet,onehasalsotostressthattheseregularitiesareonly scribed, we deliberately considered simple spaces. Enhancing
statistical:twodevelopmentaltrajectoriesareneverexactlythe the complexity of perception and motor spaces seems crucial
same,andmoreimportantly,ithappensthatsomeparticulartra- inordertoexpecttheemergenceofmorecomplexformsofbe-
jectoriesobservedinsomeexperimentsdifferqualitativelyfrom havior.However,designingsuitablespacesthatcanleadtocom-
themean.Fig.9illustratesthispoint.Thefiguresonthetop-left plexbehavioralpatternsraisesseveraldifficultissues.
and top-right corners present runs which are very typical and A firstissueiswhether perception andmotor spacesshould
correspond to the “complete scenario” described by Measure be considered as two independent spaces. The intrinsic links
1. On the contrary, the runs presented on the bottom-left and that bind perception with action have been stressed by many
bottom-rightcornerscorrespondtoatypicalresults.Theexper- authors. In some circumstances, relevant information about a
iment of which curves are presented in the bottom-left corner givenenvironment arisesfromsensorimotortrajectoriesrather
shows a case where the focused exploration of bashing was than from simple analysis of perceptual data. Several experi-
performed before the focused exploration of biting. Neverthe- ments have shown that agents can simplify problems of cat-
less,inthiscase,theregularity“nonaffordantbeforeaffordant” egorizing situations by actively modifying their own position
is preserved. On the bottom-right corner, we observe a run in ororientationwithrespecttotheenvironmentorbymodifying
whichtheaffordantbashingactivityappearsveryearlyandbe- the environment itself. In the same manner, certain environ-
foreanyotherfocusedactivity.Thisbalancebetweenstatistical mental regularities can be detected only by producing a par-
regularitiesanddiversityhasparallelsininfantsensorimotorde- ticularstereotypedbehavior(e.g.,[46]and[47]).Thefactthat
velopment[45].Therearesomestrongstructuralregularitiesbut perception is fundamentally active, naturally leads to consider

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 281
abstractionslikebehavioralschemasasrelevantunitforunder- internalschemastructures.However,thecurrentlimitationsof
standingdevelopment. such a system may appear when considering more complex
SchemasarefamouslyknownascentralelementsofPiaget’s forms of behavioral organization such as formation of hierar-
developmental psychology but the term has also been used in chicalstructuresandtheemergenceofgoals.
neurology, cognitive psychology, and motor control [48, pp. 1) Hierarchical Organization: Complex behavior patterns
36–40], and related notions appeared in artificial intelligence arehierarchicallyorganized.Forinstance,acomplexmotorpro-
undernameslikeframesorscripts[49],[50].InPiaget’stheory, gramisoftendescribedasanabstracteventsequenceatahigh
children’sdevelopmentcanbeinterpretedastheincrementalor- levelandadetailedmotorprograminalowerlevel.Therefore,
ganizationofasetofschemas.Schemasareskillsthatserveboth possibilityforforminglevelstructuresisakeyissue.Different
theperceivingoftheenvironmentandactinguponit.Piagetcalls authorshavealreadytriedtotacklehowcombinationsofprim-
assimilationtheabilitytomakesenseofasituationintermsof itives could be autonomously organized in higher level struc-
acurrentsetofschemas,andaccommodationthewayinwhich tures. Option theory offers an interesting mathematical frame-
schemasareupdatedastheexpectationsbasedonassimilation worktoaddresshierarchicalorganizationofsystemsusingex-
are not met. The child starts with basic sensorimotor schemas plicit schema structures [52]. Options are like subroutines as-
suchassuckling,grasping,andsomeprimaryformsofeye-hand sociated with closed-loop control structures. They can invoke
coordination. Through accommodation and assimilation, new other options as components. Barto et al. have recently illus-
schemas are created, and sets of existing schemas get coordi- trated in a simple environment how options could be used to
nated.Thechildmakesprogressivelymorecomplexabstractin- developahierarchicalcollectionofskills[21].Hierarchicalor-
ferencesabouttheenvironment,leadingeventuallytolanguage ganization of explicit schemas is also illustrated by the work
andlogic,formsofabstractthoughtthatarenolongerdirectly ofDrescheramongothers[51].However,canhierarchicallyor-
grounded,inparticular,sensorimotorsituations.Thewholede- ganized behavior appear in the absence of explicit schemas?
velopmentaltrajectorycanbeinterpretedasanextensionfrom Different attempts have been made in this direction. A mul-
asimplesensorimotorspacetoanelaboratedmentalspace.The tiple model-based reinforcement learning capable of decom-
spacechangesbutthefundamentaldynamicsofaccommodation posing a task based on predictability levels was proposed by
andassimilationthatactivelydrivethechild’sbehaviorremain Doyaetal.[53].TaniandNolfipresentedasystemcapableof
the same. combininglocalexpertsusinggatedmodules[54].However,in
Itisimportanttostressthatschemasareprimarilyfunctional allthesestudies,explicitlevelstructuresarepredeterminedby
units. In that sense, they are a priori distinct from structural thenetworkarchitecture.Thequestionofwhetherhierarchical
unitsthatcanbeidentifiedintheorganizationoftheorganism structurescansimplyself-organizewithoutbeingexplicitlypro-
orthemachinethatproducestheobservedbehavior.However, grammedremainsopen.
manyartificialintelligencemodelsmakeuseofinternalexplicit 2) Goal-Directedness: Complex behavior patterns are also
schemastructures.Insuchsystems,thereisaone-to-onemap- associated with intentionally directed processes. This means
pingbetweentheseinternalstructuresandthefunctionalopera- that they are performed by an agent trying to achieve a
tionthattheagentcanperform.Forinstance,Drescherdescribes particular desirable situation that constitutes its aim or goal
a system inspired by Piaget’s theories in which a developing (e.g.,reducinghunger,followingsomeone,learningsomething).
agentexplicitlycreates,modifies,andmergesschemastructures The agent’s behavior reflects his or her intention, that is the
in order to interact with a simple simulated environment [51]. planofactionthattheagentchoosesforrealizingthisparticular
Using explicit schema structures has several advantages: such goal. This plan includes both the means and the pursued goal
structurescanbemanipulatedviasymbolicoperations,creation [55]. Once again, systems using explicit schema structure
ofnewskillscanbeeasilymonitoredbyfollowingthecreation embed these notions of goals and means as explicit symbolic
ofnewschemas,etc. representations. Such explicit goals can be created, updated,
Other systems do not rely on such explicit representations. deleted, and more importantly, easily monitored. This has led
Thesearetypicallysubsymbolicsystems,usingcontinuousrep- to numerous systems in classical artificial intelligence, and
resentations of their environment. Nevertheless, such systems research in this area has influenced the importance of the
may display some organized forms of behavior where clear way we consider decision making or planning. More recently,
functional units can be identified. Their developmental trajec- research on agent architectures [56] has put a major emphasis
tories can also be interpreted as a progressive organization of on the same issues. However, these models do not give much
schemas.Forinstance,thedevelopmentaltrajectoriesproduced insight on the developmental and cognitive mechanisms that
by the typical experiments of Section VII can be interpreted lead to the notion of intentionally directed behavior. Can
as assimilation and accommodation phases. In these typical goalsandmeanssimplyemergeoutofsubsymbolicdynamics?
runs, the robot “discovers” the biting and bashing schema by This is one of the most challenging issues that developmental
producing repeated sequences of these kinds of behavior, but approaches to cognition have to face [57]. To some extent,
initially these actions are not systematically oriented towards certain reinforcement learning models have demonstrated that
the biteable or the bashable object. This stage corresponds to the organization of behavior into goals and subgoals can be
“assimilation.”Itisonlylaterthat“accommodation”occursas interpreted as emergent features resulting in simpler drives
biting and bashing starts to be associated with their respective [37]. However, no subsymbolic systems currently matches
appropriate context of use. Our experiments show that func- the performances and the flexibility of systems using explicit
tional organization can emerge even in the absence of explicit goal-directed schemas.

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
282 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
3) Generalization, Transfer, Analogy: Generalization, argue,inparticular,thatprogress-drivenlearningcouldhelpun-
transfer or analogies between schemas are also thought to be derstandwhychildrenfocusonspecificimitativeactivitiesata
central for the emergence of complex behavior patterns (see certainageandhowtheyprogressivelyorganizepreferentialin-
[58] for a general discussion of the issue of transfer in cogni- teractionswithparticularentitiespresentintheirenvironment.
tion). Skills do not develop independently from one another. 1) Progress Niches: To facilitate interpretation, we in-
Theonesthathavestructuralrelationshipbootstrapeachother. troduced the notion of progress niches to characterize the
In particular, processes of analogy and metaphors are crucial behavior of our model. The progress drive pushes the agent
fortransferringknow-howdevelopedinsensorimotorcontexts to discover and focus on situations which lead to maximal
to more abstract spaces [59]. There is an important literature learning progress. These situations, neither too predictable
on how to compare explicit schema structure (e.g., [60]), but nor too difficult to predict, are “progress niches.” Progress
many authors have argued that generalization and transfer of niches are not intrinsic properties of the environment. They
skillscouldalsobe(maybeevenmore)efficientintheabsence result from a relation between a particular environment, a
ofsymbolicrepresentation[61].Thisdebatebearssomeresem- particular embodiment (sensors, actuators, feature detectors,
blancewiththeoppositionbetweenlocalistsordistributedkinds and techniques used by the prediction algorithms), and a par-
ofrepresentation.Systemswithexplicitschemastructures,but ticular time in the developmental history of the agent. Once
also many subsymbolic systems using memories organized discovered, progress niches progressively disappear as they
into local structures (e.g., sets of experts) are called localists. become more predictable. The notion of progress niches is
In this scheme, learning a new behavior schema corresponds relatedtoVygotsky’szoneofproximaldevelopment,wherethe
totheadditionofatemplatetoanexistingsetofmodules.The adultdeliberatelychallengesthechild’slevelofunderstanding.
independence of the modules facilitates incremental learning Adults push children to engage in activities beyond their cur-
aseachadditiondoesnotcauseinterferenceswiththeexisting rent mastery level, but not too far beyond so that they remain
memory contents. However, extension to unknown patterns comprehensible [64]. We could interpret the zone of proximal
must be realized with ad hoc processes that specify the way developmentasasetofpotentialprogressnichesorganizedby
similarity should be computed. In the same manner, general- the adult in order to help the child learn. However, it should
izationacrossalargesetoflocalrepresentationsisintrinsically be clear that independently of the adults’ efforts, what is and
difficult. On the contrary, in systems with distributed repre- what is not a progress niche is ultimately defined from the
sentations, behavior schemas are not assigned to a particular child’s point of view. Progress niches also share similarities
module but are memorized in a distributed manner (e.g., as withCsikszentmihalyi’sflowexperiences[8].Csikszentmihalyi
synaptic weights of global neural network). This means that argues that some activities are autotelic when challenges are
eachschemacanonlyexistinrelationtoothers.Self-organized appropriately balanced with the skills required to cope with
generalizationprocessesarefacilitatedinsuchcontext[62]. them(seealso [65]).Weprefer tousethe term progressniche
Developmental trajectories of intrinsically motivated agents byanalogywithecologicalnichesaswerefertoatransientstate
are constrained by many factors. We have briefly discussed in the evolution of a complex “ecological” system involving
someoftheimportantissuesfordesigningsystemscapableof theembodiedagentanditsenvironment.
developing reusable, goal-directed, hierarchically organized 2) Self-OtherDistinction: Usingthisterminology,thecom-
behavioralschemas.Investigatingtheresultingdynamicsofthe putational model presented in this paper shows how an agent
intrinsic motivation systems embedded in such kinds of more can: 1) separate its sensorimotor space into zones of different
complexspaceswillbethetopicoffutureresearch. predictability levels and 2) choose to focus on the one which
leadsto maximallearning progress,called a “progressniche.”
B. RelationtoDevelopmentalPsychology
With this kind of operant model, it could be speculated that
Ourresearchtakesclearinspirationfromdevelopmentalpsy- meaningfulsensorimotor distinctions(self, others, and objects
chology both conceptually (the notion of intrinsic motivation in the environment) may be the result of discriminations con-
originallycomesfrompsychology)andmethodologically(anal- structedduringaprogress-drivenprocess.Wecanmorespecifi-
ysis of development in terms of qualitative sequences of dif- callyofferaninterpretationofseveralfundamentalstageschar-
ferentkindsofbehavioralpatterns).Couldourmodelbeinter- acterizinganinfant’sdevelopmentduringtheirfirstyear.
estinginreturnforinterpretingprocessesunderlyinganinfant’s (cid:127) Stage 1: Like-me stance (0–1 m). Simple forms of imi-
development?Moreprecisely: tative behavior have been argued to be present just after
(cid:127) Can we interpret a particular developmental process as birth. They could constitute a process of early identifica-
beingtheresultofaprogressdrive,anintrinsicmotivation tion. Some totally or partially nativist explanations could
systemdrivingtheinfantintosituationsexpectedtoresult account for this early “like-me stance” [66], [67]. This
inmaximallearningprogress? would suggest the possibility of an early distinction be-
(cid:127) Can operant models of intrinsic motivation provide tweenpersonsandthings.Ifanintermodalmappingfacil-
useful abstraction that address the complexity of infant’s itatingthematchbetweenwhatisseenandwhatisfeltex-
development? ists,thehypothesisofaprogressdrivewouldsuggestthat
Someinitialattemptshavebeentakentostartansweringthese infants will indeed create a discrimination between such
questions. Taking ground on preliminary experimental results, easily predictable couplings (interaction with peers) and
we discussed in[63] a scenariopresenting the putativeroleof unpredictablesituations(alltheothercases)andthatthey
the progress drive for the development of early imitation. We willfocusonthefirstzoneoftheirsensorimotorspacethat

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 283
constitutesa“progressniche.”Neonatesimitation(whenit anditisnotamodelofhumandevelopment.Forinstance,the
occurs)wouldbetheresultoftheexploitationofthemost playgroundexperimentfocusesdirectlyonthediscoveryofob-
predictablecouplingpresentjustafterbirth. ject’s affordances. However, in addition to the developmental
(cid:127) Stage 2: Circular reactions (1–2 m). During the first robotics engineering techniques that it explores, we think that
two months of their life, infants perform repeated body thissystem,aswellasotherexistingintrinsicartificialmotiva-
motion. They kick their legs repeatedly, they wave their tionsystems,canalsobeusedasa“toolforthoughts”indevel-
arms. This process is sometimes referred as “body bab- opmentalpsychology.Inthatsense,itmayhelpinformulating
bling.” However, nothing indicates that this exploratory newconceptsusefulfortheinterpretationofthedevelopmental
| behavior | is  | randomly | organized. |     | Rochat | argues | that |     |     |     |     |     |     |
| -------- | --- | -------- | ---------- | --- | ------ | ------ | ---- | --- | --- | --- | --- | --- | --- |
dynamicsunderlyingchildren’sdevelopment.Forexample,the
children are in fact performing self-imitation, trying to existence of a progress drive could explain why certain types
imitate themselves [68]. This would mean that children ofimitativebehaviorareproducedbychildrenatacertainage
arestructuringtheirownbehaviorinordertomakeitmore
andstopbeingproducedlateron.Itcouldalsoexplainhowdis-
predictable and form “circular reactions” this way [41], criminationbetweenactionsorientedtowardstheself,towards
| [69]. | Such self-imitative |     | behaviors |     | can be | well | explained |     |     |     |     |     |     |
| ----- | ------------------- | --- | --------- | --- | ------ | ---- | --------- | --- | --- | --- | --- | --- | --- |
others,andtowardstheenvironmentmayoccur.However,wedo
bytheprogressdrivehypothesis.Sensorimotortrajectories
notevenimaginethatadriveformaximizinglearningprogress
directed towards the child’s own body can be easily dis- couldbetheonlymotivationalprincipledrivingchildren’sde-
criminatedfromtrajectoriesdirectedtowardsotherpeople
velopment.Thecompletepictureislikelytoincludeacomplex
by comparing their relative predictability. In many re- set of drives. Developmental dynamics are certainly the result
spects,makingprogressinunderstandingprimarycircular oftheinterplaybetweenintrinsicandextrinsicformsofmotiva-
reactionsiseasierthaninthecasesinvolvingotheragents:
tions,inparticularlearningbiases,aswellasembodimentand
Self-centered types of behavior are “progress niches.” environmental constraints. We believe that computational and
| In such        | a scenario, |               | the “self” | emerges |                 | as a meaningful |      |                    |     |     |              |                  |          |
| -------------- | ----------- | ------------- | ---------- | ------- | --------------- | --------------- | ---- | ------------------ | --- | --- | ------------ | ---------------- | -------- |
|                |             |               |            |         |                 |                 |      | robotic approaches |     | can | help specify | the contribution | of these |
| discrimination |             | for achieving |            | better  | predictability. |                 | Once |                    |     |     |              |                  |          |
differentcomponentsintheoverallobservedpatternsandshed
thisdistinctionismade,progressforpredictingtheeffects newlightontheparticularroleplayedbyintrinsicmotivationin
ofself-centeredactionscanberapidlymade.
thesecomplexprocesses.
| (cid:127) Stage | 3: Self-other    |        | interactions |           | (2–4     | m). After   | two      |     |     |     |            |     |     |
| --------------- | ---------------- | ------ | ------------ | --------- | -------- | ----------- | -------- | --- | --- | --- | ---------- | --- | --- |
| months,         | infants          | become | more         | attentive |          | to the      | external |     |     |     |            |     |     |
|                 |                  |        |              |           |          |             |          |     |     | IX. | CONCLUSION |     |     |
| world           | and particularly |        | to           | people.   | Parental | scaffolding |          |     |     |     |            |     |     |
plays a critical role for making the interaction with the Intrinsicmotivationsystems arelikelytoplayapivotalrole
child more predictable [70]. Parents adapt their own forthefutureofdevelopmentalrobotics.Inthispaper,wehave
responses so that interactions with the child follow the presentedthebackgroundindevelopmentalpsychology,neuro-
| normal | social | rules | that characterize |     | communicative |     | ex- |                             |     |     |     |                          |     |
| ------ | ------ | ----- | ----------------- | --- | ------------- | --- | --- | --------------------------- | --- | --- | --- | ------------------------ | --- |
|        |        |       |                   |     |               |     |     | science,andmachinelearning. |     |     | We  | showedthatcurrentefforts |     |
changes (e.g., turn taking). Moreover,if an adultimitates in the developmental robotics community are approaching
an infant’s own actions, it can trigger continued activity the construction of intrinsic motivation systems through the
| in the | infant. | This early | imitative |     | behavior | is referred | as  |                    |     |     |                |             |         |
| ------ | ------- | ---------- | --------- | --- | -------- | ----------- | --- | ------------------ | --- | --- | -------------- | ----------- | ------- |
|        |         |            |           |     |          |             |     | operationalization |     | and | implementation | of concepts | such as |
“pseudo-imitation” by Piaget [71]. Pseudo-imitation and “novelty,” “surprise,” or more generally “curiosity.” We have
focusing on scaffolded adult behavior could be seen as reviewed some representative works in this direction, trying
predictable effects of the progress drive. As the self-cen- to classify them into different groups according to the way
tered trajectories start to become well mastered (and do theyoperationalizedcuriosity.Then,wepresentedanintrinsic
not constitute “progress niches” anymore), the child’s motivation system called IAC, which was conceived to drive
focus shifts to another branch of the discrimination tree, the development of a robot in continuous noisy inhomoge-
the“self-other”zone. neous environmental and sensorimotor spaces, permitting an
(cid:127) Stage 4: Interactions with objects (5–7 m). After five autonomousself-organizationofbehaviorintoadevelopmental
months,attentionshiftsagainfrompeopletoobjects.Chil- trajectory with sequences of increasingly complex behavioral
drengainincreasedcontroloverthemanipulationofsome patterns.Thiswasmadepossiblethankstothewaythesystem
objectsonwhichtheydiscover“affordances”[72].Parents evaluates its own learning progress, through the combination
recognizethisshiftandinitiateinteractionsaboutthoseaf- of a regional evaluation of the similarity of situations with a
fordantobjects. However,childrendonoteasilyalternate smoothingoftheerrorratecurvesassociatedtoeachregion.
their attention between the object and their caregiver. A Thissystemwastestedintworoboticsetups.Inafirstsimple
progress-drivenprocesscanaccountforthisdiscrimination simulated robotic setup, we showed in detail how the system
between affordant objects and unmastered aspects of the works, and provokes both behavioral and cognitive develop-
| environment. |     | Although | this | stage | is typically | not | seen as |     |     |     |     |     |     |
| ------------ | --- | -------- | ---- | ----- | ------------ | --- | ------- | --- | --- | --- | --- | --- | --- |
ment,bylookingindetailintothetracesofthesimulation.This
imitative, it could be argued that the exploratory process firstsetupalsoshowedhowIACcanallowarobottoavoidsit-
involvedinthediscoveryoftheobjectaffordancesshares uations which are not learnable by the system, and engage in
| several | common | features | with | the | one involved |     | for self- |            |                  |     |            |            |             |
| ------- | ------ | -------- | ---- | --- | ------------ | --- | --------- | ---------- | ---------------- | --- | ---------- | ---------- | ----------- |
|         |        |          |      |     |              |     |           | situations | of progressively |     | increasing | complexity | in terms of |
centered activities: the child structures its world looking difficultyoflearning,whichleadstoaself-organizationofthe
for“progressniches.” behavior. This first setup finally allowed us to show that our
Wehavetostressthatthesystemdiscussedinthispaperisnot intrinsic motivation system could be used efficiently as an ac-
meanttoreenactpreciselytheinfant’sdevelopmentalsequence, tivelearningalgorithmrobustininhomogeneousspaces.Some

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
284 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
currentlyongoingworksuggeststhattheseresultsstillholdin ACKNOWLEDGMENT
high-dimensional continuous spaces. If this is confirmed, this
The authors would like to thank A. Whyte whose help and
would allow us to attack real-world learning problems whose
programmingskills were precious for conducting experiments
propertiesofinhomogeneitykeptthemoutofreachofstandard
permittingtotestintrinsicmotivationsystems(inparticular,he
activelearningmethodssofar[33].Inasecondrealandmore
designed the motor primitives used by the robot in the Play-
complexroboticsetup,weshowedhowIACcandrivethedevel-
ground experiment), as well as J.-C. Baillie for letting us use
opmentofarobotthroughmorethanonedevelopmentaltransi-
tion,andthusallowstherobottoautonomouslygenerateade- theURBIsystem[73]forprogrammingtherobotandL.Steels
velopmentalsequence.Conductingtheseexperimentsalsopro- forrelevantcommentsonthiswork.
videdtheopportunitytodiscuss methodologicalissues related
totheevaluationofadevelopmentalrobot.Indeed,classicalma-
chinelearningmethodsofevaluation,basedonthemeasureof
REFERENCES
theperformanceofasystemonagivenhuman-definedtask,are [1] J.Weng,J.McClelland,A.Pentland,O.Sporns,I.Stockman,M.Sur,
notsuitedfordevelopmentalrobotssinceoneoftheirkeyfea- andE.Thelen,“Autonomousmentaldevelopmentbyrobotsandani-
mals,”Science,vol.291,pp.599–600,2001.
turesistobetask-independent,asadvocatedbyWeng[34].We
[2] M.Lungarella,G.Metta,R.Pfeifer,andG.Sandini,“Developmental
explained that a developmental evaluation should be based on robotics:Asurvey,”ConnectionSci.,vol.15,no.4,pp.151–190,2003.
themonitoringoftheevolutionofthecomplexityofthesystem [3] M. Asada, S. Noda, S. Tawaratsumida, and K. Hosoda, “Purposive
behavior acquisition on a real robot by vision-based reinforcement
fromdifferentpointsofview,sincecomplexityisindeedacon-
learning,”Mach.Learn.,vol.23,pp.279–303,1996.
ceptwhichisobserver-dependent.Forexample,itisanecessity [4] J.Elman,“Learninganddevelopmentinneuralnetworks:Theimpor-
tocoupleameasureoftheevolutionofthecomplexityfromthe tanceofstartingsmall,”Cognition,vol.48,pp.71–99,1993.
[5] R.White,“Motivationreconsidered:Theconceptofcompetence,”Psy-
robot’s point of view, and the monitoring of its behavior on a
chol.Rev.,vol.66,pp.297–333,1959.
long time scale using methods inspired from human sciences [6] E.DeciandR.Ryan,IntrinsicMotivationandSelf-Determinationin
anddevelopmentalpsychology. HumanBehavior. NewYork:Plenum,1985.
[7] D.Berlyne, Conflict, Arousal andCuriosity. NewYork: McGraw-
We have also discussed the limits of the system as we pre-
Hill,1960.
sented it in this paper. Indeed, there are two kinds of limita- [8] M.Csikszenthmihalyi,Flow-thePsychologyofOptimalExperience.
tionswhichwillbethesubjectoffuturework.Ontheonehand, NewYork:HarperPerennial,1991.
[9] W.Schultz,P.Dayan,andP.Montague,“Aneuralsubstrateofpredic-
we deliberately made the simplification that what the system tionandreward,”Science,vol.275,pp.1593–1599,1997.
should optimize is the immediate reward ( ). This al- [10] P. Dayan and W. Belleine, “Reward, motivation and reinforcement
learning,”Neuron,vol.36,pp.285–298,2002.
lowedusnottousecomplexreinforcementtechniquesandlimit
[11] S.KakadeandP.Dayan,“Dopamine:Generalizationandbonuses,”
thebiasescomingfromtheactionselectionprocedureinorder NeuralNetw.,vol.15,pp.549–559,2002.
tobetterunderstandthepropertiesofourlearningprogressmea- [12] J.-C. Horvitz, “Mesolimbocortical and nigrostriatal dopamine re-
sponsestosalient non-rewardevents,”Neuroscience,vol. 96,no.4,
sure. Nevertheless,this will be a necessity in the future to use
pp.651–656,2000.
such complex reinforcement learning techniques, since in the [13] M.Csikszentmihalyi,Creativity-FlowandthePsychologyofDiscovery
real-world progress niches are not always readily accessible, andInvention. NewYork:HarperPerennial,1996.
[14] J.Schmidhuber,“Curiousmodel-buildingcontrolsystems,”inProc.
andthuscomestheproblemsofdelayedrewards.Thisextension
Int.JointConf.NeuralNetw.,Singapore,1991,vol.2,pp.1458–1463.
ofoursystemshouldcertainlybeinspiredbytheworkofBarto [15] S. Thrun, “Exploration in active learning,” in Handbook of Brain
et al. [21] who have presented a study which is very comple- ScienceandNeuralNetworks,M.Arbib,Ed. Cambridge,MA:MIT
Press,1995.
mentarytoours,inwhichtheyexperimentedtheuseofcomplex
[16] J.Herrmann,K.Pawelzik,andT.Geisel,“Learningpredicitverepre-
reinforcementtechniquesgivenasimplenovelty-basedintrinsic sentations,”Neurocomputing,vol.32–33,pp.785–791,2000.
motivationsystem. [17] J.Weng,“Atheoryformentallydevelopingrobots,”inProc.2ndInt.
Conf.DevelopmentLearn.,2002,pp.131–140.
A second kind of limitation which characterizes the current
[18] X.HuangandJ. Weng, “Noveltyandreinforcementlearning inthe
systemisthefactthatthe sensorimotorspaceisrathersimple, value system of developmental robots,” in Proc. 2nd Int. Workshop
in particular, from the point of view of representation. It is an Epigenetic Robotics: Modeling Cognitive Development in Robotic
Systems, C. Prince, Y. Demiris, Y. Marom, H. Kozima, and C.
open issue to study how forms of representations more com-
Balkenius, Eds., 2002, vol. 94, Lund University Cognitive Studies,
plex than scalar vectors, such as schemas for example, could pp. 47–55.
beintegratedwithintheIACsystem.Oneofthepotentialprob- [19] F. Kaplan and P.-Y. Oudeyer, “Motivational principles for visual
know-how development,” in Proc. 3rd Int. Workshop Epigenetic
lemstobesolvedisifseverallevelsofrepresentationsareused:
Robotics: Modeling Cognitive Development in Robotic Systems, C.
How can one build measures of learning progress or knowl- Prince, L. Berthouze, H. Kozima, D. Bullock, G. Stojanov, and C.
edge gain which are homogeneous and allow the comparison Balkenius,Eds.,2003,vol.101,LundUniversityCognitiveStudies,
pp.73–80.
of activities or sensorimotor contexts which involve different [20] J.Marshall,D.Blank,andL.Meeden,“Anemergentframeworkfor
representations? self-motivationindevelopmentalrobotics,”inProc.3rdInt.Conf.De-
velopmentLearn.,SanDiego,CA,2004,pp.104–111.
Finally, we have seen that even if the primary goal of the
[21] A.Barto,S.Singh,andN.Chentanez,“Intrinsicallymotivatedlearning
system we presented is to allow the construction of a truly ofhierarchicalcollectionsofskills,”inProc.3rdInt.Conf.Develop-
developmental robot, taking inspiration from human devel- mentLearn.,SanDiego,CA,2004,pp.112–119.
[22] V. Fedorov, Theory of Optimal Experiment. New York, NY: Aca-
opment, the system could in return possibly be useful for
demic,1972.
developmental psychologists as a tool for thoughts. Indeed, [23] D.Cohn,Z.Ghahramani,andM.Jordan,“Activelearningwithstatis-
we explained how it can help to formulate new concepts for ticalmodels,”J.Artif.Intell.Res.,vol.4,pp.129–145,1996.
[24] M.HasenjagerandH.Ritter,ActiveLearninginNeuralNetworks.
the interpretation of the developmental dynamics involved in
Berlin,Germany:Physica-VerlagGmbH,2002,Physica-VerlagStudies
humaninfant’sdevelopment. InFuzzinessandSoftComputingSeries,pp.137–169.

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
OUDEYERetal.:INTRINSICMOTIVATIONSYSTEMSFORAUTONOMOUSMENTALDEVELOPMENT 285
[25] J.DenzlerandC.Brown,“Informationtheoreticsensordataselection [54] J.TaniandS.Nolfi,“Learningtoperceivetheworldasarticulated:An
foractiveobjectrecognitionandstateestimation,”IEEETrans.Pattern approachforhierarchicallearninginsensory-motorsystems,”Neural
Anal.Mach.Intell.,vol.2,no.24,pp.145–157,Feb.2002. Netw.,vol.12,pp.1131–1141,1999.
[26] M.PlutowskyandH.White,“Selectingconcisetrainingsetsfromclean [55] M. Tomasello, M. Carpenter, J. Call, T. Behne, and H. Moll, “Un-
data,”IEEETrans.NeuralNetw.,vol.4,no.2,pp.305–318,Mar.1993. derstandingandsharingintentions:Theoriginsofculturalcognition,”
[27] T.WatkinandA.Rau,“Selectingexamplesforperceptrons,”J.Physics Behav.BrainSci.,vol.28,no.5,pp.675–691,2005.
A:MathematicalandGeneral,vol.25,pp.113–121,1992. [56] F. Dignum and R. Conte, “Intentional agents and goal formation,”
[28] D.MacKay,“Information-basedobjectivefunctionsforactivedatase- inProc.4th Int. WorkshopIntell. AgentsIV,Agent Theories,Archi-
lection,”NeuralComput.,vol.4,pp.590–604,1992. tectures,andLanguages,London,U.K.,1997,vol.1365,LNCS,pp.
[29] M.Belue,K.Bauer,andD.Ruck,“Selectingoptimalexperimentsfor 231–243.
multipleoutputmulti-layerperceptrons,”NeuralComput.,vol.9,pp. [57] F.KaplanandV.Hafner,“Thechallengesofjointattention,”Interac-
161–183,1997. tionStudies,vol.7,no.2,pp.128–134,2006.
[30] G.PaasandJ.Kindermann,“Bayesianqueryconstructionforneural [58] A.Robins,“Transferincognition,”ConnectionSci.,vol.8,no.2,pp.
network models,” in Advances in Neural Processing Systems, G. 185–204,1996.
Tesauro,D.Touretzky,andT.Leen,Eds. :MITPress,1995,vol.7, [59] G.LakoffandM.Johnson,PhilosophyintheFlesh:TheEmbodied
pp.443–450. MindanditsChallengetoWesternThought. NewYork:BasicBooks,
[31] K.O.M.HasenjagerandH.Ritter,ActiveLearninginSelf-Organizing 1998.
Maps. NewYork:Elsevier,1999,pp.57–70. [60] D.Gentner,K.Holyoak,andN.Kokinov,TheAnalogicalMind:Per-
[32] D.Cohn,L.Atlas,andR.Ladner,“Improvinggeneralizationwithac- spectivesfromCognitiveScience. Cambridge,MA:MITPress,2001.
tivelearning,”Mach.Learn.,vol.15,no.2,pp.201–221,1994. [61] L. Pratt and B. Jennings, “A survey of connectionist networkreuse
[33] J.PolandandA.Zell,“Differentcriteriaforactivelearninginneural throughtransfer,”ConnectionSci.,vol.8,no.2,pp.163–184,1996.
networks:Acomparativestudy,”inProc.10thEur.Symp.Artif.Neural [62] J.Tani,M.Ito,andY.Sugita,“Self-organizationofdistributedlyrep-
Netw.,M.Verleysen,Ed.,2002,pp.119–124. resentedmultiplebehaviorschemainamirrorsystem,”NeuralNetw.,
[34] J.Weng,“Developmentalrobotics:Theoryandexperiments,”Int.J. vol.17,pp.1273–1289,2004.
HumanoidRobotics,vol.1,no.2,pp.199–236,2004. [63] F.KaplanandP.-Y.Oudeyer,“Theprogress-drivehypothesis:Anin-
[35] N.RoyandA.McCallum,“Towardsoptimalactivelearningthrough terpretationofearlyimitation,”inModelsandMechanismsofImitation
samplingestimationoferrorreduction,”inProc.18thInt.Conf.Mach. andSocialLearning:Behavioral,SocialandCommunicationDimen-
Learn.,2001,pp.441–448. sions,K.DautenhahnandC.Nehaniv,Eds. Cambridge,U.K.:Cam-
[36] R.CollobertandS.Bengio,“Svmtorch:Supportvectormachinesfor bridgeUniv.Press,2007,pp.361–377.
large-scale regression problems,” J. Mach. Learn. Res., vol. 1, pp. [64] L.Vygotsky,MindinSociety. Cambridge,MA:HarvardUniv.Press,
143–160,2001. 1978,TheDevelopmentofHigherPsychologicalProcesses.
[37] R.SuttonandA.Barto,ReinforcementLearning:AnIntroduction. [65] L. Steels, “The autotelic principle,” in Embodied Artificial Intelli-
Cambridge,MA.:MITPress,1998. gence,I.Fumiya,R.Pfeifer,L.Steels,andK.Kunyoshi,Eds. Berlin,
[38] C. Walkins and P. Dayan, “Q-learning,” Mach. Learn., vol. 8, pp. Germany:Springer-Verlag,2004,vol.3139,LectureNotesinAI,pp.
279–292,1992. 231–242.
[39] K. Kaneko and I. Tsuda, Complex Systems : Chaos and Beyond. [66] A.MeltzoffandA.Gopnick,“Theroleofimitationinunderstanding
Berlin,Germany:Springer-Verlag,2000. persons and developing a theory of mind,” in Understanding Other
[40] O.SpornsandT.Pegors,“Information-theoreticalaspectsofembodied Minds,H.T.-F.S.Baron-CohenandD.Cohen,Eds. Oxford,U.K.:
artificialintelligence,”inEmbodiedArtificialIntelligence,F.Iida,R. OxfordUniv.Press,1993,pp.335–366.
Pfeifer,L.Steels,andY.Kuniyoshi,Eds. Berlin,Germany:Springer- [67] C.MooreandV.Corkum,“Socialunderstandingattheendofthefirst
Verlag,2003,LNAI3139,pp.74–85. yearoflife,”DevelopmentalRev.,vol.14,pp.349–372,1994.
[41] J.Piaget,TheOriginsofIntelligenceinChildren. NewYork,NY: [68] P.Rochat,“Egofunctionofearlyimitation,”inTheImitativeMind:
Norton,1952. Development,EvolutionandBrainBases,A.MelzoffandW.Prinz,
[42] O.Michel,“Webots:Professionalmobilerobotsimulation,”Int.J.Ad- Eds. Cambridge,U.K.:CambridgeUniv.Press,2002.
vancedRoboticSyst.,vol.1,no.1,pp.39–42,2004. [69] J. Baldwin, Mental Development in the Child and the Race. New
[43] J.RekimotoandY.Ayatsuka,“Cybercode:Designingaugmentedre- York:Macmillan,1925.
alityenvironmentswithvisualtags,”inProc.DesigningAugmented [70] H.Schaffer,“Earlyinteractivedevelopmentinstudiesofmother-infant
RealityEnvironments,2000,pp.1–10. interaction,”inProc.LochLomondsSymp.,NewYork,1977,pp.3–18.
[44] S.Schaal,C.Atkeson,andS.Vijayakumar,“Scalabletechniquesfrom [71] J. Piaget, Play, Dreams and Imitation in Childhood. New York:
nonparameteric statistics for real-time robot learning,” Appl. Intell., NortonPress,1962.
vol.17,no.1,pp.49–60,2002. [72] J.Gibson,TheEcologicalApproachtoVisualPerception. Mahwah,
[45] E.ThelenandL.B.Smith,ADynamicSystemsApproachtotheDevel- NJ:LawrenceErlbaum,1986.
opmentofCognitionandAction. Cambridge,MA:MITPress,1994. [73] J.-C.Baillie,“Urbi:Towardsauniversalroboticlow-levelprogram-
[46] R. D. Beer, “The dynamics of active categorical perception in an ming language,” in Proc. IEEE Int. Conf. Intell. Robots Syst., Aug.
evolvedmodelagent,”AdaptiveBehav.,vol.11,no.4,pp.209–243, 2005,pp.820–825.
2003.
[47] S.NolfiandJ.Tani,“Extractingregularitiesinspaceandtimethrough
acascadeofpredictionnetworks,”ConnectionSci.,vol.11,no.2,pp.
129–152,1999.
[48] M. Arbib, The Handbook of Brain Theory and Neural Networks.
Cambridge,MA:MITPress,2003.
[49] M.Minsky,“Aframeworkforrepresentingknowledge,”inThePsy-
Pierre-YvesOudeyerstudiedtheoreticalcomputer
chology of Computer Vision, P. Wiston, Ed. New York: McGraw-
science at the Ecole Normale Supérieure de Lyon,
Hill,1975,pp.211–277.
Lyon,France,andreceivedthePh.D.degreeinartifi-
[50] R.SchankandR.Abelson,Scripts,Plans,GoalsandUnderstanding: cialintelligencefromtheUniversityParisVI,France.
An Inquiry into Human Knowledge Structures. Hillsdale, NJ.: HeisaResearcherattheSonyComputerScience
LawrenceErlbaum,1977. Laboratory since 1999. He has published a book,
[51] G.L.Drescher,Made-UpMinds. Cambridge,MA.:MITPress,1991. more than 50 papers in international journals and
[52] R. Sutton, D. Precup, and S. Singh, “Between MDPSs and conferences, and received several prizes for his
semi-MDPS: A framework for temporal abstraction in reinforce- workin developmentalrobotics and on the origins
mentlearning,”Artif.Intell.,vol.112,pp.181–211,1999. of language. He is interested in the mechanisms
[53] K. Doya, K. Samejima, K. Katagiri, and M. Kawato, “Multiple thatallowhumansandrobotstodevelopperceptual,
model-based reinforcement learning,” Neural Comput., vol. 14, pp. motivational,behavioral,andsocialcapabilitiestobecomecapableofsharing
1347–1369,2002. culturalrepresentations.

Oudeyer P-Y, Kaplan , F. and Hafner, V. (2007) Intrinsic Motivation Systems for Autonomous Mental Development, IEEE Transactions on Evolutionary Computation, 11(2), pp. 265--286.
286 IEEETRANSACTIONSONEVOLUTIONARYCOMPUTATION,VOL.11,NO.2,APRIL2007
Frederic Kaplan graduated as an engineer of the Verena V. Hafner completed her undergraduate
EcoleNationaleSupérieurdesTélécommunications, studiesinmathematicsandcomputerscienceinGer-
Paris, and received the Ph.D. degree in artificial many,andreceivedtheM.Res.degreeincomputer
intelligencefromtheUniversityParisVI. scienceandartificialintelligence(withDistinction)
He is a Researcher at the Ecole Polytechnique from the University of Sussex, Sussex, U.K., in
Fédérale de Lausanne (EPFL), Lausanne, Switzer- 1999,andthePh.D.degreeinnaturalsciencesfrom
land.From1997and2006,heworkedattheSony the University of Zurich, Zurich, Switzerland, in
ComputerScienceLaboratory,Paris,onthedesign 2004.
of novel approaches to robot learning and on the From2004to2005,sheworkedasanAssociate
emergenceofculturalsystemsamongmachines.He Researcher in the Developmental Robotics Group,
published two books and more than 50 articles in Sony CSL, Paris, France, and joined TU Berlin,
scientificjournals,editedbooksandpeer-reviewedproceedingsinthefieldsof Germany, in 2005, as a Senior Research Scientist. Her research interests
epigeneticrobotics,complexsystems,computationalneurosciences,ethology, includeneuralcomputationandspatialcognitionintheareaofbiorobotics,and
andevolutionarylinguistics. developmental robotics with a focus on joint attention, communication, and
interaction.

## 笔记配图

![img_001](./2007-Oudeyer-Intrinsic-Motivation.assets/img_001.png)
![img_002](./2007-Oudeyer-Intrinsic-Motivation.assets/img_002.png)
![img_003](./2007-Oudeyer-Intrinsic-Motivation.assets/img_003.png)
![img_004](./2007-Oudeyer-Intrinsic-Motivation.assets/img_004.png)
![img_005](./2007-Oudeyer-Intrinsic-Motivation.assets/img_005.png)
![img_006](./2007-Oudeyer-Intrinsic-Motivation.assets/img_006.png)
![img_007](./2007-Oudeyer-Intrinsic-Motivation.assets/img_007.png)
![img_008](./2007-Oudeyer-Intrinsic-Motivation.assets/img_008.png)
![img_009](./2007-Oudeyer-Intrinsic-Motivation.assets/img_009.png)
![img_010](./2007-Oudeyer-Intrinsic-Motivation.assets/img_010.png)
![img_011](./2007-Oudeyer-Intrinsic-Motivation.assets/img_011.png)
![img_012](./2007-Oudeyer-Intrinsic-Motivation.assets/img_012.png)
![img_013](./2007-Oudeyer-Intrinsic-Motivation.assets/img_013.png)
![img_014](./2007-Oudeyer-Intrinsic-Motivation.assets/img_014.png)
![img_015](./2007-Oudeyer-Intrinsic-Motivation.assets/img_015.png)
![img_016](./2007-Oudeyer-Intrinsic-Motivation.assets/img_016.png)
![img_017](./2007-Oudeyer-Intrinsic-Motivation.assets/img_017.png)
![img_018](./2007-Oudeyer-Intrinsic-Motivation.assets/img_018.png)
![img_019](./2007-Oudeyer-Intrinsic-Motivation.assets/img_019.png)
![img_020](./2007-Oudeyer-Intrinsic-Motivation.assets/img_020.png)
![img_021](./2007-Oudeyer-Intrinsic-Motivation.assets/img_021.png)
![img_022](./2007-Oudeyer-Intrinsic-Motivation.assets/img_022.png)
![img_023](./2007-Oudeyer-Intrinsic-Motivation.assets/img_023.png)
![img_024](./2007-Oudeyer-Intrinsic-Motivation.assets/img_024.png)
![img_025](./2007-Oudeyer-Intrinsic-Motivation.assets/img_025.png)
![img_026](./2007-Oudeyer-Intrinsic-Motivation.assets/img_026.png)
![img_027](./2007-Oudeyer-Intrinsic-Motivation.assets/img_027.png)
![img_028](./2007-Oudeyer-Intrinsic-Motivation.assets/img_028.png)
![img_029](./2007-Oudeyer-Intrinsic-Motivation.assets/img_029.png)
![img_030](./2007-Oudeyer-Intrinsic-Motivation.assets/img_030.png)
![img_031](./2007-Oudeyer-Intrinsic-Motivation.assets/img_031.png)
![img_032](./2007-Oudeyer-Intrinsic-Motivation.assets/img_032.png)
![img_033](./2007-Oudeyer-Intrinsic-Motivation.assets/img_033.png)
![img_034](./2007-Oudeyer-Intrinsic-Motivation.assets/img_034.png)
![img_035](./2007-Oudeyer-Intrinsic-Motivation.assets/img_035.png)
![img_036](./2007-Oudeyer-Intrinsic-Motivation.assets/img_036.png)
![img_037](./2007-Oudeyer-Intrinsic-Motivation.assets/img_037.png)
![img_038](./2007-Oudeyer-Intrinsic-Motivation.assets/img_038.png)
![img_039](./2007-Oudeyer-Intrinsic-Motivation.assets/img_039.png)
![img_040](./2007-Oudeyer-Intrinsic-Motivation.assets/img_040.png)
![img_041](./2007-Oudeyer-Intrinsic-Motivation.assets/img_041.png)
![img_042](./2007-Oudeyer-Intrinsic-Motivation.assets/img_042.png)
![img_043](./2007-Oudeyer-Intrinsic-Motivation.assets/img_043.png)
![img_044](./2007-Oudeyer-Intrinsic-Motivation.assets/img_044.png)
![img_045](./2007-Oudeyer-Intrinsic-Motivation.assets/img_045.png)
![img_046](./2007-Oudeyer-Intrinsic-Motivation.assets/img_046.png)
![img_047](./2007-Oudeyer-Intrinsic-Motivation.assets/img_047.png)
![img_048](./2007-Oudeyer-Intrinsic-Motivation.assets/img_048.png)
![img_049](./2007-Oudeyer-Intrinsic-Motivation.assets/img_049.png)
![img_050](./2007-Oudeyer-Intrinsic-Motivation.assets/img_050.png)
![img_051](./2007-Oudeyer-Intrinsic-Motivation.assets/img_051.png)
![img_052](./2007-Oudeyer-Intrinsic-Motivation.assets/img_052.png)
![img_053](./2007-Oudeyer-Intrinsic-Motivation.assets/img_053.png)
![img_054](./2007-Oudeyer-Intrinsic-Motivation.assets/img_054.png)
![img_055](./2007-Oudeyer-Intrinsic-Motivation.assets/img_055.png)
![img_056](./2007-Oudeyer-Intrinsic-Motivation.assets/img_056.png)
![img_057](./2007-Oudeyer-Intrinsic-Motivation.assets/img_057.png)
![img_058](./2007-Oudeyer-Intrinsic-Motivation.assets/img_058.png)
![img_059](./2007-Oudeyer-Intrinsic-Motivation.assets/img_059.png)
![img_060](./2007-Oudeyer-Intrinsic-Motivation.assets/img_060.png)
![img_061](./2007-Oudeyer-Intrinsic-Motivation.assets/img_061.png)
![img_062](./2007-Oudeyer-Intrinsic-Motivation.assets/img_062.png)
![img_063](./2007-Oudeyer-Intrinsic-Motivation.assets/img_063.png)
![img_064](./2007-Oudeyer-Intrinsic-Motivation.assets/img_064.png)
![img_065](./2007-Oudeyer-Intrinsic-Motivation.assets/img_065.png)
![img_066](./2007-Oudeyer-Intrinsic-Motivation.assets/img_066.png)
![img_067](./2007-Oudeyer-Intrinsic-Motivation.assets/img_067.png)
![img_068](./2007-Oudeyer-Intrinsic-Motivation.assets/img_068.png)
![img_069](./2007-Oudeyer-Intrinsic-Motivation.assets/img_069.png)
![img_070](./2007-Oudeyer-Intrinsic-Motivation.assets/img_070.png)
![img_071](./2007-Oudeyer-Intrinsic-Motivation.assets/img_071.png)
![img_072](./2007-Oudeyer-Intrinsic-Motivation.assets/img_072.png)
![img_073](./2007-Oudeyer-Intrinsic-Motivation.assets/img_073.png)
![img_074](./2007-Oudeyer-Intrinsic-Motivation.assets/img_074.png)
![img_075](./2007-Oudeyer-Intrinsic-Motivation.assets/img_075.png)
![img_076](./2007-Oudeyer-Intrinsic-Motivation.assets/img_076.png)
![img_077](./2007-Oudeyer-Intrinsic-Motivation.assets/img_077.png)
![img_078](./2007-Oudeyer-Intrinsic-Motivation.assets/img_078.png)
![img_079](./2007-Oudeyer-Intrinsic-Motivation.assets/img_079.png)
![img_080](./2007-Oudeyer-Intrinsic-Motivation.assets/img_080.png)
![img_081](./2007-Oudeyer-Intrinsic-Motivation.assets/img_081.png)
![img_082](./2007-Oudeyer-Intrinsic-Motivation.assets/img_082.png)
![img_083](./2007-Oudeyer-Intrinsic-Motivation.assets/img_083.png)
![img_084](./2007-Oudeyer-Intrinsic-Motivation.assets/img_084.png)
![img_085](./2007-Oudeyer-Intrinsic-Motivation.assets/img_085.png)
![img_086](./2007-Oudeyer-Intrinsic-Motivation.assets/img_086.png)
![img_087](./2007-Oudeyer-Intrinsic-Motivation.assets/img_087.png)
![img_088](./2007-Oudeyer-Intrinsic-Motivation.assets/img_088.png)
![img_089](./2007-Oudeyer-Intrinsic-Motivation.assets/img_089.png)
![img_090](./2007-Oudeyer-Intrinsic-Motivation.assets/img_090.png)
![img_091](./2007-Oudeyer-Intrinsic-Motivation.assets/img_091.png)
![img_092](./2007-Oudeyer-Intrinsic-Motivation.assets/img_092.png)
![img_093](./2007-Oudeyer-Intrinsic-Motivation.assets/img_093.png)
![img_094](./2007-Oudeyer-Intrinsic-Motivation.assets/img_094.png)
![img_095](./2007-Oudeyer-Intrinsic-Motivation.assets/img_095.png)
![img_096](./2007-Oudeyer-Intrinsic-Motivation.assets/img_096.png)
![img_097](./2007-Oudeyer-Intrinsic-Motivation.assets/img_097.png)
![img_098](./2007-Oudeyer-Intrinsic-Motivation.assets/img_098.png)
![img_099](./2007-Oudeyer-Intrinsic-Motivation.assets/img_099.png)
![img_100](./2007-Oudeyer-Intrinsic-Motivation.assets/img_100.png)
![img_101](./2007-Oudeyer-Intrinsic-Motivation.assets/img_101.png)
![img_102](./2007-Oudeyer-Intrinsic-Motivation.assets/img_102.png)
![img_103](./2007-Oudeyer-Intrinsic-Motivation.assets/img_103.png)
![img_104](./2007-Oudeyer-Intrinsic-Motivation.assets/img_104.png)
![img_105](./2007-Oudeyer-Intrinsic-Motivation.assets/img_105.png)
![img_106](./2007-Oudeyer-Intrinsic-Motivation.assets/img_106.png)
![img_107](./2007-Oudeyer-Intrinsic-Motivation.assets/img_107.png)
![img_108](./2007-Oudeyer-Intrinsic-Motivation.assets/img_108.png)
![img_109](./2007-Oudeyer-Intrinsic-Motivation.assets/img_109.png)
![img_110](./2007-Oudeyer-Intrinsic-Motivation.assets/img_110.png)
![img_111](./2007-Oudeyer-Intrinsic-Motivation.assets/img_111.png)
![img_112](./2007-Oudeyer-Intrinsic-Motivation.assets/img_112.png)
![img_113](./2007-Oudeyer-Intrinsic-Motivation.assets/img_113.png)
![img_114](./2007-Oudeyer-Intrinsic-Motivation.assets/img_114.png)
![img_115](./2007-Oudeyer-Intrinsic-Motivation.assets/img_115.png)
![img_116](./2007-Oudeyer-Intrinsic-Motivation.assets/img_116.jpeg)
![img_117](./2007-Oudeyer-Intrinsic-Motivation.assets/img_117.png)
![img_118](./2007-Oudeyer-Intrinsic-Motivation.assets/img_118.png)
![img_119](./2007-Oudeyer-Intrinsic-Motivation.assets/img_119.png)
![img_120](./2007-Oudeyer-Intrinsic-Motivation.assets/img_120.png)
![img_121](./2007-Oudeyer-Intrinsic-Motivation.assets/img_121.png)
![img_122](./2007-Oudeyer-Intrinsic-Motivation.assets/img_122.png)
![img_123](./2007-Oudeyer-Intrinsic-Motivation.assets/img_123.png)
![img_124](./2007-Oudeyer-Intrinsic-Motivation.assets/img_124.png)
![img_125](./2007-Oudeyer-Intrinsic-Motivation.assets/img_125.png)
![img_126](./2007-Oudeyer-Intrinsic-Motivation.assets/img_126.png)
![img_127](./2007-Oudeyer-Intrinsic-Motivation.assets/img_127.png)
![img_128](./2007-Oudeyer-Intrinsic-Motivation.assets/img_128.png)
![img_129](./2007-Oudeyer-Intrinsic-Motivation.assets/img_129.png)
