# Sieci neuronowe typu GAN — Transkrypt prezentacji
**Maciej Lesniak & Bartlomiej Obrochta**
Sztuczne Sieci Neuronowe | 22 kwietnia 2026

---

> **Legenda ról:**
> `[MACIEJ]` — Maciej Lesniak
> `[BARTEK]` — Bartlomiej Obrochta

---

## Slajd 1 / 22 — Tytułowy

> *~30 sekund*

Cześć wszystkim, nazywamy się Maciej i Bartłomiej i dzisiaj opowiemy wam o Generative Adversarial Networks — czyli sieciach GAN. Będziemy mówić na zmianę, a na końcu pokażemy wam live demo w notebooku Jupyter.

---

## Slajd 2 / 22 — Plan prezentacji

### `[MACIEJ]`

> *~1 minuta*

Żebyście wiedzieli czego się spodziewać, szybki przegląd tego co dzisiaj omówimy. Zaczniemy od historii — skąd w ogóle wziął się pomysł na GANy i dlaczego był taki przełomowy. Potem zbudujemy sobie intuicję przez prostą analogię, żebyście zrozumieli o co chodzi zanim dojdziemy do matematyki. Następnie omówimy architekturę, formalizm matematyczny i jak konkretnie wygląda trening krok po kroku. Potem powiemy o problemach, na które można się natknąć — bo GANy to nie jest łatwa technologia — i o wariantach, które te problemy rozwiązują. Na końcu zastosowania, kwestie etyczne i demo. Pytania możecie zadawać na bieżąco.

---

## Slajd 3 / 22 — Rys historyczny

### `[MACIEJ]`

> *~3 minuty*

Żeby zrozumieć dlaczego GANy były takie rewolucyjne, trzeba wiedzieć co było wcześniej.

Modele generatywne — czyli takie, które potrafią tworzyć nowe dane — istniały już od dawna. Restricted Boltzmann Machines, które rozwijał Hinton w latach dwutysięcznych, potrafiły generować dane, ale żeby cokolwiek z nich wyciągnąć, trzeba było stosować bardzo skomplikowane metody próbkowania zwane MCMC — Markov Chain Monte Carlo. To działało, ale było powolne i trudne w użyciu.

W 2013 roku pojawił się VAE — Variational Autoencoder, zaproponowany przez Kingsma i Wellinga. VAE to elegancki model z enkoderem, który kompresuje dane do małego wektora, i dekoderem, który z tego wektora odtwarza obraz. Problem był jednak taki, że generowane obrazy wychodziły rozmyte. Dlaczego? Bo VAE używa funkcji straty, która uśrednia wartości pikselowe — i ta średnia wizualnie wychodzi jako rozmycie.

Wtedy Ian Goodfellow, który był wtedy doktorantem na Université de Montréal pod opieką Yoshuy Bengio, zaproponował coś zupełnie innego. Zamiast modelować rozkład prawdopodobieństwa danych w sposób jawny — co jest matematycznie trudne — postawił na rywalizację dwóch sieci neuronowych, które uczą się wzajemnie przez rywalizację.

Jest taka anegdota, że Goodfellow wpadł na ten pomysł rozmawiając z kolegami w barze. Wrócił do domu, zakodował prototyp w jedną noc i zadziałało od razu. W uczeniu maszynowym to jest naprawdę wyjątkowe — tu prototypy rzadko kiedy działają za pierwszym razem.

Yann LeCun, który jest szefem AI w Meta i laureatem nagrody Turinga — czyli odpowiednika Nobla w informatyce — powiedział mniej więcej w 2016 roku, że GANy to najciekawszy pomysł w ML ostatnich dziesięciu lat.

I to widać na osi czasu — GAN pojawił się w 2014, a już rok później mieliśmy DCGAN, potem WGAN w 2017, StyleGAN w 2018. Tempo było bardzo szybkie.

MCMC-dowiedzieć się
VAE-dowiedzieć się

---

## Slajd 4 / 22 — Intuicja: fałszerz i policjant

### `[BARTEK]`

> *~3 minuty*

Zanim wejdziemy w matematykę, zbudujmy sobie intuicję przez prostą analogię.

Wyobraźcie sobie fałszerza banknotów i policjanta. Fałszerz drukuje podrobione banknoty i próbuje je przemycić. Policjant dostaje banknoty — część prawdziwe, część fałszywe — i musi je odróżnić. Na początku fałszerz jest kiepski i policjant go łatwo łapie. Ale fałszerz uczy się na swoich błędach. Widzi że policjant go złapał, więc następnym razem robi lepszy banknot. Policjant z kolei musi nadążać — bo jak fałszerz zaczyna robić lepsze banknoty, stara technika weryfikacji przestaje działać.

I tak obaj się doskonalą nawzajem przez rywalizację.

W GAN-ie fałszywy banknotem jest Generator, a policjantem jest Discriminator. Generator przyjmuje na wejście losowy szum — wyobraźcie sobie ciąg losowych liczb — i przekształca go w dane, na przykład obraz. Discriminator dostaje obrazy, część prawdziwe z zestawu treningowego, część wygenerowane przez Generator, i musi powiedzieć, które są prawdziwe a które fałszywe.

Jest tutaj jedna bardzo ważna asymetria: Generator **nigdy** nie widzi prawdziwych danych bezpośrednio. Jedyna informacja, którą dostaje, to ocena Discriminatora — czyli feedback w stylu "oszukałeś go" albo "nie oszukałeś". I na podstawie tego feedbacku uczy się generować coraz lepsze obrazy.

To nie jest współpraca — to czysta rywalizacja. Ale paradoksalnie, właśnie dlatego oba modele stają się coraz lepsze.

---

## Slajd 5 / 22 — Generator vs. Discriminator

### `[BARTEK]`

> *~2 minuty*

Żeby to formalnie poukładać — w uczeniu maszynowym mamy dwa rodzaje modeli.

Modele dyskryminacyjne, takie jak klasyczny klasyfikator, uczą się granicy decyzyjnej. Patrząc na obraz, uczą się odpowiadać na pytanie: "czy to jest kot czy pies?". Nie interesuje ich jak te dane wyglądają od środka — interesuje ich tylko jak je sklasyfikować.

Modele generatywne działają inaczej — próbują nauczyć się rozkładu danych. Chcą zrozumieć jak dane "wyglądają" statystycznie, żeby potem móc generować nowe, podobne przykłady.

GAN jest unikalny, bo łączy oba podejścia w jednym systemie. Generator jest modelem generatywnym — uczy się jak generować realistyczne dane. Discriminator jest modelem dyskryminacyjnym — uczy się odróżniać prawdziwe od fałszywych. I te dwa modele są ze sobą sprzężone, bo uczą się jeden od drugiego.

---

## Slajd 6 / 22 — Architektura GAN

### `[MACIEK]`

> *~3 minuty*

Zobaczmy teraz jak to wygląda konkretnie w praktyce.

Wszystko zaczyna się od wektora szumu **z** — to jest po prostu wektor losowych liczb, zwykle ze stu elementów, losowanych z rozkładu normalnego. To jest punkt startowy Generatora.

Generator bierze ten wektor i przepuszcza go przez sieć neuronową. Na wyjściu dostajemy obraz — na przykład dla MNIST to jest obraz 28 na 28 pikseli. Na początku treningu ten obraz wygląda jak losowy szum wizualny, bo Generator jeszcze niczego się nie nauczył.

Ten wygenerowany obraz, plus prawdziwe obrazy z zestawu treningowego, trafiają do Discriminatora. Discriminator to jest klasyfikator binarny — na wyjściu daje liczbę między zerem a jedynką. Zero oznacza "to jest fałszywe", jeden oznacza "to jest prawdziwe".

Teraz kluczowa część: ocena Discriminatora wraca jako sygnał uczenia do Generatora. Jeśli Discriminator powiedział "fałszywe" — Generator wie, że musi się poprawić. Jeśli powiedział "prawdziwe" — Generator wie, że dobrze robi.

I to się powtarza naprzemiennie: najpierw trenujemy Discriminatora żeby lepiej wykrywał fałszywe, potem trenujemy Generatora żeby lepiej oszukiwał Discriminatora.

---

## Slajd 7 / 22 — Formalizm matematyczny

### `[MACIEJ]`

> *~4 minuty*

To jest najtrudniejszy slajd — ale spróbujmy go przejść spokojnie.

Całą grę między Generatorem a Discriminatorem można zapisać jednym wyrażeniem matematycznym, które nazywamy **value function** V(D, G):

```
min_G  max_D  V(D,G) = E_x[log D(x)] + E_z[log(1 − D(G(z)))]
```

Co to znaczy? Rozbijmy na dwie części.

Pierwsza część, `E_x[log D(x)]`, to oczekiwana wartość logarytmu z oceny Discriminatora na prawdziwych danych. Discriminator chce żeby D(x) było bliskie 1 — bo to są prawdziwe dane — więc log D(x) powinien być bliski zera, czyli jak największy.

Druga część, `E_z[log(1 − D(G(z)))]`, dotyczy fałszywych danych. D(G(z)) to ocena Discriminatora na wygenerowanym obrazie. Discriminator chce żeby to było bliskie zera — czyli chce wykryć fałszywkę — więc `1 − D(G(z))` jest bliskie jedności, a log jest bliski zera, znowu jak największy. Generator natomiast chce odwrotnie — chce żeby D(G(z)) było bliskie 1, czyli chce żeby Discriminator pomylił się i uznał fałszywkę za prawdziwą.

Stąd `max_D` — Discriminator chce maksymalizować — i `min_G` — Generator chce minimalizować.

Jest jeden ważny trik w praktyce. Na początku treningu Generator jest kiepski, więc Discriminator łatwo go rozgryza — D(G(z)) jest bliskie zera. W tej sytuacji gradient `log(1 − D(G(z)))` jest bardzo mały, bo ta funkcja jest płaska przy x=0. Generator prawie nic się nie uczy. Żeby to naprawić, zamiast minimalizować `log(1 − D(G(z)))`, Generator **maksymalizuje** `log(D(G(z)))`. Matematycznie to prowadzi do tego samego minimum, ale gradient na początku treningu jest dużo silniejszy.

Punkt optymalny tej gry to tak zwana równowaga Nasha — kiedy Generator generuje dane nieodróżnialne od prawdziwych, i Discriminator musi losować (D(x) = 0.5), bo nie umie odróżnić jednych od drugich.

---

## Slajd 8 / 22 — Trening krok po kroku

### `[BARTEK]`

> *~4 minuty*

Powiedzieliśmy jak to wygląda w teorii — zobaczmy teraz konkretny algorytm, który zaraz zobaczycie w notebooku.

Każda iteracja treningu składa się z dwóch kroków.

**Krok pierwszy: trenujemy Discriminatora.** Generator jest zamrożony — nie aktualizujemy jego wag. Bierzemy mini-batch prawdziwych obrazów z zestawu treningowego i nadajemy im etykietę 1. Generujemy tyle samo fałszywych obrazów przez Generator i nadajemy im etykietę 0. Discriminator dostaje oba batche razem i liczymy Binary Cross Entropy Loss. Robimy backpropagation, ale aktualizujemy wagi **tylko** Discriminatora.


**Krok drugi: trenujemy Generatora.** Teraz Discriminator jest zamrożony. Generujemy nowy batch fałszywych obrazów, ale tym razem bez `.detach()`. I tu jest kluczowy trik — nadajemy tym fałszywym obrazom etykietę **1**, nie 0. Czemu? Bo chcemy nauczyć Generatora generowania takich obrazów, które Discriminator uzna za prawdziwe. Jeśli Discriminator daje fałszywemu obrazowi ocenę 0.3 zamiast 1.0, to jest to sygnał dla Generatora że musi się poprawić. Robimy backpropagation przez Discriminatora do Generatora i aktualizujemy tylko wagi Generatora.

Obie sieci mają osobne optymizatory — tu standardowo używa się Adam z learning rate 0.0002. I to się powtarza przez wiele epok.

---

## Slajd 9 / 22 — Mode Collapse

### `[BARTEK]`

> *~3 minuty*

Brzmi proste — ale w praktyce trening GANów jest trudny. Zacznijmy od najczęstszego problemu, czyli **mode collapse**.

Wyobraźcie sobie, że trenujemy GAN na MNIST — chcemy żeby Generator umiał generować cyfry od 0 do 9. Ale Generator jest sprytny na swój sposób — szuka DOWOLNEGO wyjścia, które minimalizuje jego funkcję straty. I wyobraźcie sobie, że odkrywa, że generowanie samych jedynek daje mu stosunkowo dobry wynik, bo jedynka to prosta kreska i Discriminator ma z nią problem. Zamiast eksplorować cały przestrzeń możliwych cyfr, Generator "kolapsuje" do jednego trybu — generuje same jedynki.

To jest właśnie mode collapse. Generator przestaje być różnorodny — zamiast modelować cały rozkład danych, skupia się na jednym lub kilku przykładach, które działają dobrze.

Są różne metody żeby z tym walczyć. **Minibatch discrimination** polega na tym, że dodajemy do Discriminatora warstwę, która porównuje próbki w całym mini-batchu naraz — jeśli wszystkie wyglądają identycznie, Discriminator dostaje sygnał że coś jest nie tak i daje karę. **Feature matching** zmienia funkcję straty Generatora tak, żeby nie optymalizował wyjścia Discriminatora, tylko próbował dopasować statystyki pośrednich warstw Discriminatora. I jest jeszcze fundamentalne rozwiązanie przez zmianę samej metryki — o tym za chwilę przy WGAN.

---

## Slajd 10 / 22 — Inne problemy i metryki

### `[BARTEK]`

> *~3 minuty*

Mode collapse to jeden problem, ale jest ich więcej.

Drugi duży problem to **niestabilność treningu**. W klasycznej sieci neuronowej mamy jedną funkcję straty i obserwujemy jak maleje — jak maleje, to wiemy że sieć się uczy. W GANach mamy dwie sieci z osobnymi funkcjami straty, które walczą ze sobą. Te dwa lossy mogą oscylować w górę i w dół bez wyraźnego postępu — i z samego wykresu lossy bardzo ciężko ocenić, czy model idzie w dobrą stronę.

Trzeci problem to **zanikające gradienty**. Jeśli Discriminator nauczy się za szybko i za dobrze, to dla każdego wygenerowanego obrazu jego ocena D(G(z)) jest bliska zera. Gradient funkcji `log(1 − D(G(z)))` przy wartościach bliskich zera jest bardzo mały — prawie płaski. Generator dostaje minimalny sygnał gradientu i praktycznie przestaje się uczyć. To jest jeden z powodów, dla których używamy non-saturating loss, o którym mówił Maciej.

Skoro przy stracie jesteśmy — jak w ogóle ocenić czy GAN się dobrze nauczył? To jest zaskakująco trudne pytanie. Dwie standardowe metryki to **Inception Score** z 2016 roku i **FID** — Fréchet Inception Distance z 2017 roku. FID jest dziś standardem — bierze pretrenowaną sieć Inception, przepuszcza przez nią prawdziwe i wygenerowane obrazy, i porównuje statystyki uzyskanych reprezentacji. Im niższe FID, tym bliżej do siebie są rozkłady prawdziwych i fałszywych danych — czyli tym lepszy model.

---

## Slajd 11 / 22 — DCGAN

### `[MACIEJ]`

> *~3 minuty*

Oryginalny GAN z 2014 roku działał na małych, prostych danych. Żeby GANy zaczęły generować realistyczne obrazy w sensownej rozdzielczości, potrzeba było DCGAN z 2015 roku — Deep Convolutional GAN od Radford, Metz i Chintali.

Kluczowa innowacja to zastąpienie w pełni połączonych warstw splotami. Generator zaczyna od małego wektora — powiedzmy 4 na 4 piksele z dużą liczbą kanałów — i przez **transposed convolutions**, czyli splot wsteczny, zwiększa rozdzielczość krok po kroku aż do pełnowymiarowego obrazu. Discriminator robi odwrotnie — używa **strided convolutions** z krokiem 2, żeby zmniejszać rozdzielczość zamiast korzystać z MaxPooling. Sieć sama uczy się jak redukować wymiar, zamiast robić to na sztywno.

Dwie inne ważne rzeczy: **Batch Normalization** po każdej warstwie — bez niej trening był bardzo niestabilny — i odpowiedni dobór aktywacji: ReLU w Generatorze, LeakyReLU w Discriminatorze, Tanh na wyjściu Generatora.

Ale najefektowniejszy wynik DCGAN to tak zwana **arytmetyka w przestrzeni latentnej**. Wyobraźcie sobie, że trenujemy na zdjęciach twarzy. Bierzemy kilka zdjęć mężczyzny w okularach i uśredniamy ich wektory z. Odejmujemy uśredniony wektor z dla mężczyzny bez okularów. Dodajemy wektor dla kobiety bez okularów. I na wyjściu dostajemy twarz kobiety w okularach. Przestrzeń latentna nie jest losowa — ma sensowną geometrię, gdzie podobne koncepcje leżą blisko siebie.

---

## Slajd 12 / 22 — CGAN i Pix2Pix

### `[BARTEK]`

> *~3 minuty*

Standardowy GAN ma jeden problem — nie możemy powiedzieć mu co ma wygenerować. Dostajemy losowy output. CGAN, czyli Conditional GAN, rozwiązuje to przez dodanie informacji warunkowej.

Pomysł jest prosty: na wejście Generatora podajemy nie tylko losowy szum z, ale też dodatkową informację y — na przykład etykietę klasy. Dla MNIST byłby to one-hot vector mówiący "chcę cyfrę 7". Generator uczy się że ten dodatkowy wektor determinuje co ma wygenerować. Discriminator też dostaje tę informację — ocenia nie tylko czy obraz wygląda realistycznie, ale też czy odpowiada podanej etykiecie.

**Pix2Pix** to rozszerzenie tego pomysłu do tłumaczenia obrazów. Zamiast etykiety klasy, warunkiem jest cały obraz wejściowy. Klasyczny przykład: mapy konturowe budynków na wejściu, realistyczne zdjęcia budynków na wyjściu. Albo szkice twarzy na wejściu, realistyczne twarze na wyjściu.

Pix2Pix wprowadził dwie ważne innowacje. Po pierwsze **U-Net** jako architektura Generatora — ma skip connections, które przenoszą szczegółowe informacje z wczesnych warstw do późnych, dzięki czemu generowane obrazy zachowują detale. Po drugie **PatchGAN** jako Discriminator — zamiast oceniać cały obraz naraz, ocenia niezależnie małe patche 70 na 70 pikseli. To pozwala lepiej wychwytywać lokalne tekstury.

Ograniczenie Pix2Pix jest jedno: potrzebuje sparowanych danych — dla każdego obrazu wejściowego musimy mieć odpowiadający mu obraz wyjściowy. To jest droge w przygotowaniu. CycleGAN za chwilę rozwiąże ten problem.

---

## Slajd 13 / 22 — WGAN

### `[MACIEJ]`

> *~3 minuty*

WGAN to jeden z najważniejszych postępów teoretycznych i praktycznych w historii GANów.

Żeby zrozumieć dlaczego WGAN powstał, musimy wrócić do problemu znikających gradientów z wcześniej. Oryginalny GAN minimalizuje dywergencję Jensena-Shannona między rozkładem prawdziwych danych a rozkładem generatora. Problem matematyczny jest taki, że kiedy dwa rozkłady nie mają dużo wspólnego — co na początku treningu jest typowe — ta dywergencja jest stałą wartością log(2) niezależnie od tego jak daleko od siebie są te rozkłady. Nie daje żadnej informacji o tym w którą stronę się poruszać. Brak gradientów.

WGAN zamienia tę metrykę na **Earth Mover's Distance**, zwaną też odległością Wassersteina. Intuicja: wyobraźcie sobie dwa kopce piasku o różnych kształtach. Earth Mover's Distance pyta: ile pracy trzeba włożyć — w sensie masa razy odległość — żeby przekształcić jeden kopiec w drugi. Ta metryka jest ciągła i informatywna nawet gdy rozkłady leżą daleko od siebie i się nie nakładają.

W praktyce to wymaga kilku zmian. Po pierwsze, Discriminator jest teraz nazywany **Critic** — nie daje już prawdopodobieństwa z sigmoidy, tylko surowy skalar bez ograniczeń. Po drugie, funkcja straty zmienia postać. Po trzecie, żeby spełnić matematyczne wymagania odległości Wassersteina, Critic musi być funkcją Lipschitza — w oryginalnym WGAN zapewnia się to przez obcinanie wag do małego przedziału, a w późniejszym WGAN-GP przez dodanie kary za gradient. 

Efekty są wyraźne: stabilniejszy trening, brak mode collapse, i co ważne — wartość funkcji straty Critica faktycznie koreluje z jakością generowanych obrazów, czyli w końcu mamy sensowny wskaźnik postępu treningu.

---

## Slajd 14 / 22 — StyleGAN

### `[BARTEK]`

> *~3 minuty*

StyleGAN z 2018 roku od NVIDIA to moment, w którym GANy zaczęły generować twarze nieodróżnialne od prawdziwych zdjęć.

Kluczowa innowacja to sposób w jaki podawany jest wektor latentny. W DCGAN wektor z trafia bezpośrednio do pierwszej warstwy Generatora. Problem: przestrzeń Z jest "splątana" — jedna współrzędna wektora jednocześnie kontroluje wiele różnych cech twarzy, co utrudnia kontrolowaną edycję.

StyleGAN dodaje **mapping network** — osiem w pełni połączonych warstw, które przekształcają wektor z w inny wektor w. Ta nowa przestrzeń W jest "rozplątana" — jej kierunki odpowiadają pojedynczym, niezależnym cechom jak wiek, kolor włosów, czy uśmiech.

Ten wektor w nie trafia do pierwszej warstwy — zamiast tego modyfikuje **każdą warstwę konwolucyjną** przez mechanizm zwany AdaIN. I tu jest piękna właściwość: różne warstwy kontrolują różne skale cech. Niskie warstwy, pracujące na małych mapach cech, kontrolują ogólny kształt twarzy i pozę. Średnie warstwy kontrolują cechy twarzy jak nos czy usta. Najwyższe warstwy, pracujące na pełnej rozdzielczości, kontrolują kolor skóry i teksturę.

To pozwala na **style mixing** — można wziąć wektor w osoby A dla niskich warstw i wektor w osoby B dla wysokich warstw, i dostać twarz o strukturze A z kolorystyką B.

Jeśli chcecie zobaczyć efekty na żywo — thispersondoesnotexist.com generuje twarze StyleGAN-em w czasie rzeczywistym. Żadna z tych osób nie istnieje.

---

## Slajd 15 / 22 — CycleGAN

### `[MACIEJ]`

> *~3 minuty*

CycleGAN z 2017 roku rozwiązuje główne ograniczenie Pix2Pix — potrzebę sparowanych danych.

Wyobraźcie sobie że chcemy nauczyć modelu zamieniać zdjęcia koni w zdjęcia zebr. Żeby zrobić to Pix2Pixem, potrzebowalibyśmy tysięcy par zdjęć — to samo miejsce, ta sama chwila, ale raz koń a raz zebra. To jest oczywiście niemożliwe do zebrania.

CycleGAN potrzebuje tylko dwóch niepowiązanych zbiorów zdjęć: jednego ze zdjęciami koni i jednego ze zdjęciami zebr. Nie muszą w żaden sposób do siebie pasować.

Jak to działa? Zamiast dwóch sieci, mamy cztery. Generator G zamienia konie w zebry, Generator F zamienia zebry w konie. Discriminator D_Y ocenia czy obrazy wyglądają jak zebry, Discriminator D_X ocenia czy wyglądają jak konie.

Kluczowy pomysł to **cycle consistency**. Jeśli weźmiemy zdjęcie konia, zamienimy je na zebrę przez G, a potem tę zebrę z powrotem na konia przez F — powinniśmy dostać z powrotem TEGO SAMEGO konia. Matematycznie: norma F(G(x)) minus x powinna być bliska zeru. To samo w drugą stronę.

Bez tego warunku model mógłby robić cokolwiek z obrazem — zamieniać każdego konia w identyczną zebrę niezależnie od tego jak wyglądał oryginalny koń. Cycle consistency wymusza, żeby transformacja zachowała istotne cechy obrazu.

---

## Slajd 16 / 22 — Zastosowania

### `[BARTEK]`

> *~3 minuty*

GANy znalazły zastosowanie w bardzo wielu dziedzinach.

Najbardziej spektakularne to **generowanie realistycznych obrazów** — twarze, sceny, produkty. Komercyjnie używa się tego do generowania zdjęć modelek dla sklepów internetowych bez sesji fotograficznych.

**Super-resolution** to jeden z najważniejszych przypadków użycia. SRGAN i ESRGAN potrafią zwiększyć rozdzielczość obrazu i dodać detale, których nie było — efekty są ostrzejsze niż tradycyjne metody interpolacji. Używa się tego do poprawy starych zdjęć i filmów.

**Image inpainting** — uzupełnianie brakujących fragmentów obrazu — zasilało funkcję Content-Aware Fill w Photoshopie.

W **medycynie** GANy generują syntetyczne obrazy MRI, co jest ważne gdy prawdziwych danych treningowych jest za mało albo są wrażliwe prywacowo. W **fizyce cząstek** CERN używa CaloGAN do symulowania detektorów — generowanie syntetycznych zderzeń jest dużo szybsze niż pełna symulacja Monte Carlo.

**Drug discovery** — MolGAN generuje nowe struktury molekularne z pożądanymi właściwościami chemicznymi.

---

## Slajd 17 / 22 — GAN vs. Diffusion Models

### `[MACIEJ]`

> *~3 minuty*

Uczciwe pytanie, które pewnie się nasuwa: skoro GANy były takie rewolucyjne, dlaczego DALL-E czy Stable Diffusion używają diffusion models, a nie GANów?

Diffusion models generują lepsze, bardziej różnorodne obrazy i mają stabilniejszy trening. Nie cierpią na mode collapse tak jak GANy. Jakość wyjścia — mierzona FID-em — jest wyraźnie lepsza.

Ale mają jedną dużą wadę: są powolne. Żeby wygenerować jeden obraz, model musi przejść od 50 do tysiąca kroków denoising — każdy krok to forward pass przez dużą sieć. Może to trwać sekundy.

GAN generuje obraz w jednym forward passie — milisekundy. I ta różnica ma znaczenie wszędzie tam, gdzie potrzebujemy generowania w czasie rzeczywistym: gry, video, interaktywne aplikacje. Super-resolution w grach — AMD FidelityFX SR — to de facto GAN. Hybrydy, gdzie GAN destyluje wiedzę z modelu diffusion, są aktywnie badane.

GANy nie są martwe — znalazły swoją niszę wszędzie tam, gdzie szybkość jest ważniejsza niż maksymalna jakość.

---

## Slajd 18 / 22 — Etyka i deepfakes

### `[BARTEK]`

> *~2 minuty*

Nie możemy mówić o GANach bez powiedzenia kilku słów o etyce.

Deepfake'i — realistyczne fałszywe nagrania wideo — to jedno z najbardziej niepokojących zastosowań tej technologii. Zagrożenia są realne: szantaż, manipulacja polityczna przez wkładanie słów w usta polityków, pornografia bez zgody ofiary.

Szerszy problem to dezinformacja wizualna. Kiedy zdjęcia i filmy można łatwo sfabrykować, zaufanie do mediów wizualnych jako dowodu na cokolwiek się załamuje.

Jest też problem biasu — GANy uczą się z danych, więc jeśli dane są uprzedzone, model też będzie uprzedzony. StyleGAN trenowany na niereprezentacyjnych danych generuje twarze jednej grupy demograficznej lepiej niż innej.

Dobra wiadomość jest taka, że te same techniki adversarialne, które tworzą deepfake'i, można wykorzystać do ich wykrywania. Detektory deepfake'ów to aktywna dziedzina badań. Digital watermarking pozwala ukryć w wygenerowanym obrazie niewidoczne oznaczenie jego sztucznego pochodzenia.

EU AI Act z 2024 roku wymaga oznaczania treści generowanych przez AI — to regulacyjna odpowiedź na te problemy.

---

## Slajd 21 / 22 — Podsumowanie

### `[MACIEJ]`

> *~2 minuty + dyskusja*

Podsumowując: GAN to system dwóch sieci neuronowych uczących się przez rywalizację. Generator produkuje fałszywe dane, Discriminator uczy się je wykrywać, i ten wyścig sprawia że Generator z czasem generuje coraz bardziej realistyczne próbki.

Trening jest niestabilny — mode collapse, znikające gradienty — ale mamy na to konkretne rozwiązania: WGAN zmienia metrykę na Earth Mover's Distance i rozwiązuje problemy gradientowe u podstaw.

Warianty — DCGAN, CGAN, StyleGAN, CycleGAN — każdy rozszerzył możliwości w innym kierunku: jakość obrazów, kontrola nad outputem, fotorealizm, brak potrzeby sparowanych danych.

Zastosowania są szerokie: medycyna, nauka, przemysł kreatywny. Ale razem z możliwościami idą realne zagrożenia związane z deepfake'ami i dezinformacją.

Wreszcie — diffusion models przejęły prowadzenie w badaniach po 2020, ale GANy pozostają niezastąpione tam gdzie liczy się szybkość.

Chętnie odpowiemy na pytania.

---

## Slajd 22 / 22 — Bibliografia

*Podziękujcie za uwagę i otwórzcie dyskusję.*

---

*Łączny szacowany czas: ~45–50 minut*
