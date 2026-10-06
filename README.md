# TESIS-
PAGINA WEB PARA EL TEMA DE TESIS 
import { useEffect, useMemo, useState } from "react";
import {
  BookOpen,
  CheckCircle2,
  ClipboardCheck,
  ExternalLink,
  FileText,
  Globe2,
  GraduationCap,
  Home,
  Layers3,
  Leaf,
  MapPinned,
  Microscope,
  Satellite,
  Search,
  ShieldCheck,
  Sparkles,
} from "lucide-react";
import {
  CircleMarker,
  MapContainer,
  Popup,
  TileLayer,
  useMap,
} from "react-leaflet";
import "leaflet/dist/leaflet.css";

import fondoEcuador from "@/assets/fondo-ecuador.jpg";
import unidadDosImg from "@/assets/unidad-2-ecuador.jpg";
import unidadTresImg from "@/assets/unidad-3-ecuador.jpg";
import { Button } from "@/components/ui/button";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { cn } from "@/lib/utils";

const COLORS = {
  jungle: "#15803d",
  ocean: "#0369a1",
  paramo: "#b45309",
};

const glassCard =
  "rounded-3xl border border-white/15 bg-white/10 shadow-2xl shadow-black/10 backdrop-blur-md";

const softGlassCard =
  "rounded-2xl border border-white/10 bg-white/[0.07] backdrop-blur-md";

type Tone = "jungle" | "ocean" | "paramo";

type TheoryTopic = {
  title: string;
  eyebrow: string;
  description: string;
  points: string[];
  tone: Tone;
};

type Guide = {
  number: number;
  title: string;
  description: string;
  objective: string;
  steps: string[];
  tool: string;
  url: string;
  tone: Tone;
};

type SiteOption = {
  id: string;
  name: string;
  category: string;
  description: string;
  position: [number, number];
  zoom: number;
  color: string;
};

const unitTwoTopics: TheoryTopic[] = [
  {
    title: "Diversidad de las especies",
    eyebrow: "Tema 2.1",
    description:
      "La megadiversidad del Ecuador se relaciona con su posición ecuatorial, la cordillera de los Andes, la influencia oceánica y la gran variedad de gradientes altitudinales y climáticos.",
    points: [
      "Costa, Sierra, Amazonía y región Insular presentan condiciones ecológicas contrastantes.",
      "Los gradientes altitudinales generan cambios rápidos de temperatura, humedad y vegetación.",
      "Los SIG permiten comparar patrones espaciales de biodiversidad con variables ambientales.",
    ],
    tone: "jungle",
  },
  {
    title: "Flora y fauna del Ecuador",
    eyebrow: "Tema 2.2",
    description:
      "La distribución de flora y fauna cambia según ecosistemas, altitud, clima, disponibilidad de agua, conectividad y estado de conservación del hábitat.",
    points: [
      "Bosques húmedos, páramos, manglares, bosques secos y ecosistemas insulares sostienen comunidades distintas.",
      "La cartografía temática ayuda a relacionar especies, ecosistemas y áreas protegidas.",
      "La lectura espacial permite pasar de memorizar especies a interpretar dónde y por qué se distribuyen.",
    ],
    tone: "jungle",
  },
  {
    title: "Especies endémicas",
    eyebrow: "Tema 2.3",
    description:
      "Una especie endémica posee una distribución natural restringida a una región determinada. El aislamiento geográfico de islas, valles y montañas favorece procesos de diferenciación y endemismo.",
    points: [
      "Galápagos constituye un caso emblemático para estudiar aislamiento y endemismo.",
      "Las barreras geográficas pueden limitar el intercambio entre poblaciones.",
      "Los mapas permiten visualizar distribuciones restringidas y reconocer hábitats prioritarios.",
    ],
    tone: "jungle",
  },
  {
    title: "Amenazas y extinción",
    eyebrow: "Tema 2.4",
    description:
      "La pérdida de biodiversidad responde a presiones que pueden analizarse espacialmente, como transformación del hábitat, fragmentación, contaminación, especies invasoras y cambio climático.",
    points: [
      "La pérdida y fragmentación del hábitat reducen disponibilidad de refugio y conectividad.",
      "Las presiones humanas pueden superponerse con zonas de alta biodiversidad.",
      "El análisis temporal de imágenes satelitales ayuda a reconocer cambios del territorio.",
    ],
    tone: "paramo",
  },
];

const unitThreeTopics: TheoryTopic[] = [
  {
    title: "Marco legal y normativa ecuatoriana",
    eyebrow: "Tema 3.1",
    description:
      "La conservación de la biodiversidad en Ecuador se sustenta en la Constitución de 2008, el Código Orgánico del Ambiente y su reglamento, además de instrumentos internacionales ratificados por el país.",
    points: [
      "La Constitución reconoce derechos de la Naturaleza y principios vinculados al patrimonio natural.",
      "El Código Orgánico del Ambiente organiza el marco general de gestión ambiental.",
      "CDB, CITES y Ramsar complementan la protección mediante compromisos internacionales.",
    ],
    tone: "ocean",
  },
  {
    title: "Sistema Nacional de Áreas Protegidas (SNAP)",
    eyebrow: "Tema 3.2",
    description:
      "El SNAP articula áreas protegidas y estrategias de gestión destinadas a conservar ecosistemas, especies, paisajes y procesos ecológicos representativos del Ecuador.",
    points: [
      "La gestión se apoya en zonificación, planes de manejo y objetivos de conservación.",
      "La información geográfica permite estudiar límites, conectividad y presiones alrededor de cada área.",
      "Los visores oficiales facilitan la exploración territorial de las áreas protegidas.",
    ],
    tone: "ocean",
  },
  {
    title: "Conservación in situ y ex situ",
    eyebrow: "Tema 3.3",
    description:
      "La conservación in situ protege especies y procesos ecológicos en su ambiente natural; la conservación ex situ resguarda individuos o material biológico fuera del hábitat cuando se requiere apoyo complementario.",
    points: [
      "In situ: áreas protegidas, corredores ecológicos y conservación de hábitats críticos.",
      "Ex situ: bancos de semillas, centros de rescate, colecciones y programas de reproducción.",
      "Las dos estrategias son complementarias y deben responder a objetivos de conservación claros.",
    ],
    tone: "ocean",
  },
  {
    title: "Estrategias de manejo y participación",
    eyebrow: "Tema 3.4",
    description:
      "La conservación moderna combina planificación territorial, restauración, conectividad ecológica, monitoreo, educación y participación de comunidades, instituciones y gobiernos locales.",
    points: [
      "Los corredores y zonas de amortiguamiento ayudan a reducir el aislamiento entre hábitats.",
      "La restauración ecológica busca recuperar funciones y conectividad.",
      "La gobernanza y la participación social fortalecen la sostenibilidad de las acciones de manejo.",
    ],
    tone: "paramo",
  },
];

const unitTwoGuides: Guide[] = [
  {
    number: 1,
    title: "Guía 1: Modelamiento de Nichos",
    description:
      "Explora la relación espacial entre una especie seleccionada y las condiciones del territorio mediante una lectura guiada en Google Earth.",
    objective:
      "Relacionar distribución, altitud, cobertura y características ambientales sin saturar el mapa con múltiples elementos.",
    steps: [
      "Selecciona una sola especie o caso de estudio.",
      "Ubica su zona de referencia y observa relieve, cobertura y contexto geográfico.",
      "Registra tres variables espaciales que puedan relacionarse con su presencia.",
      "Formula una explicación breve sobre las condiciones del hábitat observado.",
    ],
    tool: "Google Earth",
    url: "https://earth.google.com/web/",
    tone: "jungle",
  },
  {
    number: 2,
    title: "Guía 2: Análisis Temporal",
    description:
      "Compara imágenes de distintos momentos para identificar transformaciones visibles del territorio y discutir posibles amenazas a la biodiversidad.",
    objective:
      "Reconocer cambios espaciales y temporales en un área delimitada mediante observación satelital guiada.",
    steps: [
      "Selecciona un solo sector de estudio.",
      "Carga imágenes de dos o más fechas comparables.",
      "Identifica cambios de cobertura, expansión urbana, incendios u otras señales visibles.",
      "Relaciona los cambios observados con posibles consecuencias para la biodiversidad.",
    ],
    tool: "NASA Worldview",
    url: "https://worldview.earthdata.nasa.gov/",
    tone: "paramo",
  },
];

const unitThreeGuides: Guide[] = [
  {
    number: 3,
    title: "Guía 3: Análisis Cartográfico SNAP",
    description:
      "Utiliza el visor ambiental para reconocer límites, contexto territorial y elementos cartográficos de un área protegida seleccionada.",
    objective:
      "Interpretar una sola área protegida a la vez y relacionar su localización con el territorio circundante.",
    steps: [
      "Selecciona una única área protegida.",
      "Activa únicamente las capas necesarias para la pregunta de trabajo.",
      "Observa límites, accesos, coberturas y elementos cercanos.",
      "Redacta una interpretación cartográfica de la zona analizada.",
    ],
    tool: "Visor MAATE",
    url: "http://ide.ambiente.gob.ec/mapainteractivo/",
    tone: "ocean",
  },
  {
    number: 4,
    title: "Guía 4: Conflictos Territoriales",
    description:
      "Examina elementos antrópicos cercanos a una zona de conservación y plantea posibles relaciones entre uso del territorio y manejo ambiental.",
    objective:
      "Identificar presiones territoriales cercanas a una zona de conservación mediante lectura cartográfica focalizada.",
    steps: [
      "Selecciona una sola zona de estudio.",
      "Identifica carreteras, asentamientos u otros elementos antrópicos cercanos.",
      "Describe posibles interacciones entre esos elementos y la conservación del área.",
      "Propón una medida de prevención, manejo o monitoreo.",
    ],
    tool: "OpenStreetMap",
    url: "https://www.openstreetmap.org/",
    tone: "paramo",
  },
];

const mapSites: SiteOption[] = [
  {
    id: "yasuni",
    name: "Parque Nacional Yasuní",
    category: "Área protegida amazónica",
    description:
      "Referencia didáctica para observar cobertura boscosa, ríos y contexto territorial amazónico.",
    position: [-0.68, -76.4],
    zoom: 8,
    color: COLORS.jungle,
  },
  {
    id: "galapagos",
    name: "Parque Nacional Galápagos — Santa Cruz",
    category: "Área protegida insular",
    description:
      "Referencia didáctica para estudiar aislamiento geográfico, paisajes volcánicos y conservación insular.",
    position: [-0.64, -90.33],
    zoom: 9,
    color: COLORS.ocean,
  },
  {
    id: "cajas",
    name: "Parque Nacional Cajas",
    category: "Área protegida altoandina",
    description:
      "Referencia didáctica para reconocer lagunas, relieve glaciar y ecosistemas de páramo.",
    position: [-2.78, -79.24],
    zoom: 10,
    color: COLORS.paramo,
  },
  {
    id: "podocarpus",
    name: "Parque Nacional Podocarpus",
    category: "Área protegida andino-amazónica",
    description:
      "Referencia didáctica para explorar gradientes altitudinales y conectividad entre ecosistemas.",
    position: [-4.13, -79.16],
    zoom: 9,
    color: COLORS.jungle,
  },
  {
    id: "cotopaxi",
    name: "Parque Nacional Cotopaxi",
    category: "Área protegida altoandina",
    description:
      "Referencia didáctica para analizar volcanismo, páramo y usos del territorio alrededor de un área protegida.",
    position: [-0.68, -78.44],
    zoom: 10,
    color: COLORS.paramo,
  },
];

const bibliography = [
  {
    reference:
      "Alcántara, J., & Medina, S. (2019). El uso de los itinerarios didácticos (SIG) en la educación ambiental. Enseñanza de las Ciencias, 37(2), 173–188.",
    url: "https://doi.org/10.5565/rev/ensciencias.2258",
  },
  {
    reference:
      "Bearman, N., Jones, N., André, I., Cachinho, H. A., & DeMers, M. (2016). The future role of GIS education in creating critical spatial thinkers. Journal of Geography in Higher Education, 40(3), 394–408.",
    url: "https://doi.org/10.1080/03098265.2016.1144729",
  },
  {
    reference:
      "Fast, V., & Hossain, F. (2020). An Alternative to Desktop GIS? Evaluating the Cartographic and Analytical Capabilities of WebGIS Platforms for Teaching. The Cartographic Journal, 57(2), 175–186.",
    url: "https://doi.org/10.1080/00087041.2019.1631514",
  },
  {
    reference:
      "Kholoshyn, I., Nazarenko, T., Bondarenko, O., Hanchuk, O., & Varfolomyeyeva, I. (2021). The application of geographic information systems in schools around the world: A retrospective analysis. Journal of Physics: Conference Series, 1840(1), 012017.",
    url: "https://doi.org/10.1088/1742-6596/1840/1/012017",
  },
  {
    reference:
      "Kim, M., & Bednarz, R. (2013). Development of critical spatial thinking through GIS learning. Journal of Geography in Higher Education, 37(3), 350–366.",
    url: "https://doi.org/10.1080/03098265.2013.769091",
  },
  {
    reference:
      "Mestanza, C. R., et al. (2020). In-Situ and Ex-Situ Biodiversity Conservation in Ecuador: A Review of Policies, Actions and Challenges. Diversity, 12(8), 315.",
    url: "https://doi.org/10.3390/d12080315",
  },
  {
    reference:
      "Schulze, U. (2021). “GIS works!”—But why, how, and for whom? Findings from a systematic review. Transactions in GIS, 25(2), 768–804.",
    url: "https://doi.org/10.1111/tgis.12704",
  },
  {
    reference:
      "Walshe, N. (2017). Developing trainee teacher practice with geographical information systems (GIS). Journal of Geography in Higher Education.",
    url: "https://doi.org/10.1080/03098265.2017.1331209",
  },
];

function toneClasses(tone: Tone) {
  if (tone === "jungle") {
    return {
      badge: "border-emerald-300/25 bg-emerald-400/10 text-emerald-100",
      icon: "bg-emerald-400/15 text-emerald-100",
      button: "bg-[#15803d] text-white hover:bg-[#166534]",
      border: "border-emerald-300/20",
    };
  }

  if (tone === "ocean") {
    return {
      badge: "border-sky-300/25 bg-sky-400/10 text-sky-100",
      icon: "bg-sky-400/15 text-sky-100",
      button: "bg-[#0369a1] text-white hover:bg-[#075985]",
      border: "border-sky-300/20",
    };
  }

  return {
    badge: "border-amber-300/25 bg-amber-400/10 text-amber-100",
    icon: "bg-amber-400/15 text-amber-100",
    button: "bg-[#b45309] text-white hover:bg-[#92400e]",
    border: "border-amber-300/20",
  };
}

function SectionTitle({
  icon: Icon,
  kicker,
  title,
  description,
}: {
  icon: typeof Home;
  kicker: string;
  title: string;
  description: string;
}) {
  return (
    <div className="mb-8 flex flex-col gap-4 md:mb-10 md:flex-row md:items-end md:justify-between">
      <div className="max-w-3xl">
        <div className="mb-3 flex items-center gap-2 text-sm font-semibold uppercase tracking-[0.2em] text-white/60">
          <Icon className="h-4 w-4" aria-hidden="true" />
          {kicker}
        </div>
        <h2 className="text-3xl font-black tracking-tight text-white md:text-5xl">{title}</h2>
      </div>
      <p className="max-w-xl text-sm leading-7 text-white/70 md:text-base">{description}</p>
    </div>
  );
}

function TheoryCard({ topic }: { topic: TheoryTopic }) {
  const tone = toneClasses(topic.tone);

  return (
    <article className={cn(softGlassCard, tone.border, "p-5 sm:p-6")}>
      <div className="mb-4 flex items-start justify-between gap-4">
        <div>
          <span
            className={cn(
              "inline-flex rounded-full border px-3 py-1 text-xs font-bold uppercase tracking-wider",
              tone.badge,
            )}
          >
            {topic.eyebrow}
          </span>
          <h4 className="mt-3 text-xl font-bold text-white">{topic.title}</h4>
        </div>
        <div className={cn("grid h-10 w-10 shrink-0 place-items-center rounded-2xl", tone.icon)}>
          <Leaf className="h-5 w-5" aria-hidden="true" />
        </div>
      </div>

      <p className="text-sm leading-7 text-white/70">{topic.description}</p>

      <ul className="mt-5 space-y-3">
        {topic.points.map((point) => (
          <li key={point} className="flex items-start gap-3 text-sm leading-6 text-white/80">
            <CheckCircle2 className="mt-1 h-4 w-4 shrink-0 text-white/60" aria-hidden="true" />
            <span>{point}</span>
          </li>
        ))}
      </ul>
    </article>
  );
}

function GuideCard({ guide }: { guide: Guide }) {
  const tone = toneClasses(guide.tone);

  return (
    <article className={cn(glassCard, tone.border, "overflow-hidden p-6 sm:p-7")}>
      <div className="flex flex-col gap-5">
        <div className="flex items-start justify-between gap-4">
          <div className={cn("grid h-12 w-12 place-items-center rounded-2xl", tone.icon)}>
            <ClipboardCheck className="h-6 w-6" aria-hidden="true" />
          </div>
          <span className={cn("rounded-full border px-3 py-1 text-xs font-bold", tone.badge)}>
            Hoja de trabajo {String(guide.number).padStart(2, "0")}
          </span>
        </div>

        <div>
          <h4 className="text-xl font-bold leading-snug text-white">{guide.title}</h4>
          <p className="mt-3 text-sm leading-7 text-white/70">{guide.description}</p>
        </div>

        <div className="rounded-2xl border border-white/10 bg-black/10 p-4">
          <p className="text-xs font-black uppercase tracking-[0.18em] text-white/50">Objetivo</p>
          <p className="mt-2 text-sm leading-6 text-white/80">{guide.objective}</p>
        </div>

        <div>
          <p className="mb-3 text-xs font-black uppercase tracking-[0.18em] text-white/50">
            Secuencia de trabajo
          </p>
          <ol className="space-y-3">
            {guide.steps.map((step, index) => (
              <li key={step} className="flex gap-3 text-sm leading-6 text-white/80">
                <span className="grid h-6 w-6 shrink-0 place-items-center rounded-full bg-white/10 text-xs font-black text-white">
                  {index + 1}
                </span>
                <span>{step}</span>
              </li>
            ))}
          </ol>
        </div>

        <Button asChild className={cn("h-12 w-full rounded-2xl font-bold", tone.button)}>
          <a href={guide.url} target="_blank" rel="noopener noreferrer">
            <Satellite className="mr-2 h-4 w-4" aria-hidden="true" />
            Abrir {guide.tool}
            <ExternalLink className="ml-2 h-4 w-4" aria-hidden="true" />
          </a>
        </Button>
      </div>
    </article>
  );
}

function UnitHero({
  image,
  number,
  title,
  description,
  tone,
}: {
  image: string;
  number: string;
  title: string;
  description: string;
  tone: Tone;
}) {
  const overlay =
    tone === "jungle"
      ? "from-[#052e16]/95 via-[#15803d]/65"
      : "from-[#082f49]/95 via-[#0369a1]/65";

  return (
    <div className="relative mb-8 overflow-hidden rounded-3xl border border-white/15 shadow-2xl">
      <img src={image} alt="" aria-hidden="true" className="h-64 w-full object-cover md:h-72" />
      <div className={cn("absolute inset-0 bg-gradient-to-r to-black/10", overlay)} />
      <div className="absolute inset-0 flex items-end p-6 sm:p-8 md:p-10">
        <div className="max-w-3xl">
          <p className="text-xs font-black uppercase tracking-[0.22em] text-white/70">Unidad {number}</p>
          <h3 className="mt-2 text-3xl font-black text-white md:text-4xl">{title}</h3>
          <p className="mt-3 max-w-2xl text-sm leading-7 text-white/80 md:text-base">{description}</p>
        </div>
      </div>
    </div>
  );
}

function FlyToSelected({ site }: { site: SiteOption | null }) {
  const map = useMap();

  useEffect(() => {
    if (!site) {
      map.flyTo([-1.8312, -78.1834], 6, { duration: 1.1 });
      return;
    }

    map.flyTo(site.position, site.zoom, { duration: 1.25 });
  }, [map, site]);

  return null;
}

function Index() {
  const [selectedSiteId, setSelectedSiteId] = useState("");

  const selectedSite = useMemo(
    () => mapSites.find((site) => site.id === selectedSiteId) ?? null,
    [selectedSiteId],
  );

  return (
    <div className="relative min-h-screen overflow-hidden bg-[#06111f] text-white">
      {/* Fondo líquido dinámico */}
      <div className="pointer-events-none fixed inset-0 overflow-hidden" aria-hidden="true">
        <div
          className="absolute -left-28 -top-20 h-[30rem] w-[30rem] animate-pulse rounded-full opacity-45 blur-3xl"
          style={{ backgroundColor: COLORS.jungle }}
        />
        <div
          className="absolute -right-24 top-[18%] h-[32rem] w-[32rem] animate-pulse rounded-full opacity-35 blur-3xl [animation-delay:700ms]"
          style={{ backgroundColor: COLORS.ocean }}
        />
        <div
          className="absolute bottom-[-10rem] left-[25%] h-[34rem] w-[34rem] animate-pulse rounded-full opacity-30 blur-3xl [animation-delay:1200ms]"
          style={{ backgroundColor: COLORS.paramo }}
        />
        <div className="absolute inset-0 bg-[radial-gradient(circle_at_top,rgba(255,255,255,0.08),transparent_42%)]" />
      </div>

      <div className="relative z-10">
        <header className="border-b border-white/10 bg-black/10 px-4 py-5 backdrop-blur-xl sm:px-8">
          <div className="mx-auto flex max-w-7xl items-center justify-between gap-5">
            <div className="flex items-center gap-4">
              <div className="grid h-12 w-12 place-items-center rounded-2xl border border-white/15 bg-white/10 shadow-lg backdrop-blur-md">
                <Leaf className="h-6 w-6 text-emerald-200" aria-hidden="true" />
              </div>
              <div>
                <p className="text-xs font-black uppercase tracking-[0.28em] text-emerald-200/80">BIOSIG</p>
                <h1 className="mt-1 text-xl font-black tracking-tight text-white sm:text-2xl">
                  Biodiversidad + Sistemas de Información Geográfica
                </h1>
              </div>
            </div>
            <div className="hidden rounded-full border border-white/10 bg-white/[0.06] px-4 py-2 text-xs font-semibold text-white/60 lg:block">
              Recurso didáctico para la formación docente
            </div>
          </div>
        </header>

        <Tabs defaultValue="inicio" className="w-full">
          {/* NAVEGACIÓN PRINCIPAL: exactamente 5 secciones */}
          <div className="sticky top-0 z-[1000] border-b border-white/10 bg-[#06111f]/70 px-3 py-3 backdrop-blur-xl sm:px-6">
            <TabsList className="mx-auto grid h-auto max-w-7xl grid-cols-2 gap-2 rounded-2xl border border-white/10 bg-white/[0.06] p-2 lg:grid-cols-5">
              <TabsTrigger
                value="inicio"
                className="min-h-12 rounded-xl px-3 py-3 text-white/65 data-[state=active]:bg-white/15 data-[state=active]:text-white data-[state=active]:shadow-lg"
              >
                🏠 Inicio
              </TabsTrigger>
              <TabsTrigger
                value="panel"
                className="min-h-12 rounded-xl px-3 py-3 text-white/65 data-[state=active]:bg-white/15 data-[state=active]:text-white data-[state=active]:shadow-lg"
              >
                📖 Panel Pedagógico
              </TabsTrigger>
              <TabsTrigger
                value="mapa"
                className="min-h-12 rounded-xl px-3 py-3 text-white/65 data-[state=active]:bg-white/15 data-[state=active]:text-white data-[state=active]:shadow-lg"
              >
                🗺️ Mapa Interactivo
              </TabsTrigger>
              <TabsTrigger
                value="evaluacion"
                className="min-h-12 rounded-xl px-3 py-3 text-white/65 data-[state=active]:bg-white/15 data-[state=active]:text-white data-[state=active]:shadow-lg"
              >
                📝 Evaluación
              </TabsTrigger>
              <TabsTrigger
                value="bibliografia"
                className="col-span-2 min-h-12 rounded-xl px-3 py-3 text-white/65 data-[state=active]:bg-white/15 data-[state=active]:text-white data-[state=active]:shadow-lg lg:col-span-1"
              >
                📚 Bibliografía
              </TabsTrigger>
            </TabsList>
          </div>

          {/* 1. INICIO */}
          <TabsContent value="inicio" className="m-0 focus-visible:outline-none">
            <main className="mx-auto max-w-7xl px-4 py-10 sm:px-8 md:py-14">
              <section className="relative mb-10 overflow-hidden rounded-[2rem] border border-white/15 shadow-2xl">
                <img
                  src={fondoEcuador}
                  alt="Paisaje representativo del Ecuador utilizado como portada de BIOSIG"
                  className="absolute inset-0 h-full w-full object-cover"
                />
                <div className="absolute inset-0 bg-gradient-to-r from-[#04190d]/95 via-[#062b24]/80 to-[#0369a1]/50" />
                <div className="relative max-w-4xl px-6 py-14 sm:px-10 md:px-14 md:py-20">
                  <div className="mb-5 inline-flex items-center gap-2 rounded-full border border-white/15 bg-white/10 px-4 py-2 text-xs font-bold uppercase tracking-[0.2em] text-white/80 backdrop-blur-md">
                    <Globe2 className="h-4 w-4" aria-hidden="true" />
                    Introducción a los SIG
                  </div>
                  <h2 className="text-4xl font-black leading-tight tracking-tight text-white md:text-6xl">
                    Comprender el territorio para enseñar biodiversidad
                  </h2>
                  <p className="mt-6 max-w-3xl text-base leading-8 text-white/80 md:text-lg">
                    Los Sistemas de Información Geográfica (SIG) constituyen un conjunto potente de herramientas para
                    <strong className="font-bold text-white"> recolectar, almacenar, transformar, analizar y representar datos geoespaciales</strong>
                    del mundo real. En educación, permiten convertir información territorial compleja en experiencias visuales,
                    comparables e interpretables.
                  </p>
                </div>
              </section>

              <SectionTitle
                icon={Home}
                kicker="Del SIG de escritorio al WebGIS"
                title="Tecnología geoespacial más accesible"
                description="La evolución hacia plataformas WebGIS permite explorar información geográfica desde un navegador, reduciendo barreras técnicas y facilitando su incorporación en actividades de enseñanza y aprendizaje."
              />

              <div className="grid gap-6 lg:grid-cols-2">
                <div className={cn(glassCard, "p-6 sm:p-8")}>
                  <div className="mb-5 grid h-12 w-12 place-items-center rounded-2xl bg-emerald-400/15 text-emerald-100">
                    <Layers3 className="h-6 w-6" aria-hidden="true" />
                  </div>
                  <h3 className="text-2xl font-black text-white">SIG de escritorio</h3>
                  <p className="mt-4 text-sm leading-7 text-white/70">
                    Tradicionalmente, gran parte del trabajo SIG se concentró en programas instalados en computadoras, con procesos de carga,
                    edición y análisis de capas geográficas. Estas herramientas siguen siendo fundamentales para análisis avanzados.
                  </p>
                </div>

                <div className={cn(glassCard, "p-6 sm:p-8")}>
                  <div className="mb-5 grid h-12 w-12 place-items-center rounded-2xl bg-sky-400/15 text-sky-100">
                    <Globe2 className="h-6 w-6" aria-hidden="true" />
                  </div>
                  <h3 className="text-2xl font-black text-white">WebGIS</h3>
                  <p className="mt-4 text-sm leading-7 text-white/70">
                    Las plataformas WebGIS trasladan la exploración cartográfica a la web. El estudiante puede observar, comparar y consultar
                    información espacial desde el navegador, lo que favorece actividades guiadas con menor carga técnica inicial.
                  </p>
                </div>
              </div>

              <div className="mt-12">
                <SectionTitle
                  icon={Satellite}
                  kicker="Herramientas WebGIS"
                  title="Tres plataformas para aprender con el territorio"
                  description="BIOSIG integra herramientas externas con propósitos didácticos específicos. Cada actividad debe enfocarse en una pregunta y un área de estudio concreta."
                />

                <div className="grid gap-6 lg:grid-cols-3">
                  <div className={cn(glassCard, "p-6")}>
                    <div className="grid h-11 w-11 place-items-center rounded-2xl bg-emerald-400/15 text-emerald-100">
                      <Globe2 className="h-5 w-5" aria-hidden="true" />
                    </div>
                    <h3 className="mt-5 text-xl font-black">Google Earth</h3>
                    <p className="mt-3 text-sm leading-7 text-white/70">
                      Úsalo para explorar relieve, cobertura, distancias, ubicación y contexto paisajístico. Es especialmente útil para introducir
                      relaciones espaciales antes de pasar a análisis más complejos.
                    </p>
                    <Button asChild className="mt-5 w-full rounded-2xl bg-[#15803d] hover:bg-[#166534]">
                      <a href="https://earth.google.com/web/" target="_blank" rel="noopener noreferrer">
                        Abrir Google Earth <ExternalLink className="ml-2 h-4 w-4" />
                      </a>
                    </Button>
                  </div>

                  <div className={cn(glassCard, "p-6")}>
                    <div className="grid h-11 w-11 place-items-center rounded-2xl bg-amber-400/15 text-amber-100">
                      <Satellite className="h-5 w-5" aria-hidden="true" />
                    </div>
                    <h3 className="mt-5 text-xl font-black">NASA Worldview</h3>
                    <p className="mt-3 text-sm leading-7 text-white/70">
                      Permite visualizar imágenes satelitales y comparar fechas. En BIOSIG se utiliza para observar cambios territoriales y discutir
                      posibles amenazas ambientales desde una perspectiva temporal.
                    </p>
                    <Button asChild className="mt-5 w-full rounded-2xl bg-[#b45309] hover:bg-[#92400e]">
                      <a href="https://worldview.earthdata.nasa.gov/" target="_blank" rel="noopener noreferrer">
                        Abrir NASA Worldview <ExternalLink className="ml-2 h-4 w-4" />
                      </a>
                    </Button>
                  </div>

                  <div className={cn(glassCard, "p-6")}>
                    <div className="grid h-11 w-11 place-items-center rounded-2xl bg-sky-400/15 text-sky-100">
                      <MapPinned className="h-5 w-5" aria-hidden="true" />
                    </div>
                    <h3 className="mt-5 text-xl font-black">Visor interactivo MAATE</h3>
                    <p className="mt-3 text-sm leading-7 text-white/70">
                      El visor del Ministerio del Ambiente, Agua y Transición Ecológica permite consultar información territorial oficial. Para una
                      actividad didáctica, activa únicamente las capas necesarias y analiza una zona a la vez.
                    </p>
                    <Button asChild className="mt-5 w-full rounded-2xl bg-[#0369a1] hover:bg-[#075985]">
                      <a href="http://ide.ambiente.gob.ec/mapainteractivo/" target="_blank" rel="noopener noreferrer">
                        Abrir visor MAATE <ExternalLink className="ml-2 h-4 w-4" />
                      </a>
                    </Button>
                  </div>
                </div>
              </div>
            </main>
          </TabsContent>

          {/* 2. PANEL PEDAGÓGICO */}
          <TabsContent value="panel" className="m-0 focus-visible:outline-none">
            <main className="mx-auto max-w-7xl px-4 py-10 sm:px-8 md:py-14">
              <SectionTitle
                icon={BookOpen}
                kicker="Panel pedagógico"
                title="Teoría organizada por unidades"
                description="La información se presenta por bloques breves. Cada unidad integra inmediatamente sus hojas de trabajo SIG para conectar teoría, observación territorial y análisis."
              />

              <Tabs defaultValue="unidad-2" className="w-full">
                <TabsList className="mb-8 grid h-auto w-full grid-cols-1 gap-2 rounded-2xl border border-white/10 bg-white/[0.06] p-2 md:grid-cols-2">
                  <TabsTrigger
                    value="unidad-2"
                    className="min-h-14 rounded-xl px-4 py-3 text-white/65 data-[state=active]:bg-[#15803d]/80 data-[state=active]:text-white"
                  >
                    Unidad 2 · Ecuador, País Megadiverso
                  </TabsTrigger>
                  <TabsTrigger
                    value="unidad-3"
                    className="min-h-14 rounded-xl px-4 py-3 text-white/65 data-[state=active]:bg-[#0369a1]/80 data-[state=active]:text-white"
                  >
                    Unidad 3 · Conservación de la Biodiversidad
                  </TabsTrigger>
                </TabsList>

                <TabsContent value="unidad-2" className="m-0 focus-visible:outline-none">
                  <UnitHero
                    image={unidadDosImg}
                    number="2"
                    title="Ecuador, País Megadiverso"
                    description="Explora cómo el territorio, los ecosistemas y las presiones ambientales influyen en la distribución y conservación de la biodiversidad ecuatoriana."
                    tone="jungle"
                  />

                  <div className="grid gap-5 lg:grid-cols-2">
                    {unitTwoTopics.map((topic) => (
                      <TheoryCard key={topic.title} topic={topic} />
                    ))}
                  </div>

                  <div className="mt-12 border-t border-white/10 pt-10">
                    <div className="mb-7 flex items-center gap-3">
                      <div className="grid h-11 w-11 place-items-center rounded-2xl bg-emerald-400/15 text-emerald-100">
                        <ClipboardCheck className="h-5 w-5" aria-hidden="true" />
                      </div>
                      <div>
                        <p className="text-xs font-black uppercase tracking-[0.2em] text-white/50">Guías integradas</p>
                        <h3 className="text-2xl font-black text-white">Aplicación SIG · Unidad 2</h3>
                      </div>
                    </div>
                    <div className="grid gap-6 lg:grid-cols-2">
                      {unitTwoGuides.map((guide) => (
                        <GuideCard key={guide.number} guide={guide} />
                      ))}
                    </div>
                  </div>
                </TabsContent>

                <TabsContent value="unidad-3" className="m-0 focus-visible:outline-none">
                  <UnitHero
                    image={unidadTresImg}
                    number="3"
                    title="Conservación de la Biodiversidad"
                    description="Relaciona normativa, áreas protegidas, estrategias de conservación y manejo territorial con herramientas de análisis espacial."
                    tone="ocean"
                  />

                  <div className="grid gap-5 lg:grid-cols-2">
                    {unitThreeTopics.map((topic) => (
                      <TheoryCard key={topic.title} topic={topic} />
                    ))}
                  </div>

                  <div className="mt-12 border-t border-white/10 pt-10">
                    <div className="mb-7 flex items-center gap-3">
                      <div className="grid h-11 w-11 place-items-center rounded-2xl bg-sky-400/15 text-sky-100">
                        <ClipboardCheck className="h-5 w-5" aria-hidden="true" />
                      </div>
                      <div>
                        <p className="text-xs font-black uppercase tracking-[0.2em] text-white/50">Guías integradas</p>
                        <h3 className="text-2xl font-black text-white">Aplicación SIG · Unidad 3</h3>
                      </div>
                    </div>
                    <div className="grid gap-6 lg:grid-cols-2">
                      {unitThreeGuides.map((guide) => (
                        <GuideCard key={guide.number} guide={guide} />
                      ))}
                    </div>
                  </div>
                </TabsContent>
              </Tabs>
            </main>
          </TabsContent>

          {/* 3. MAPA INTERACTIVO */}
          <TabsContent value="mapa" className="m-0 focus-visible:outline-none">
            <main className="mx-auto max-w-7xl px-4 py-10 sm:px-8 md:py-14">
              <SectionTitle
                icon={MapPinned}
                kicker="Mapa interactivo"
                title="Exploración satelital focalizada"
                description="Selecciona únicamente un punto de estudio. BIOSIG evita mostrar todos los elementos simultáneamente para reducir la sobrecarga visual y favorecer el análisis progresivo."
              />

              <div className="grid gap-6 xl:grid-cols-[340px_minmax(0,1fr)]">
                <aside className={cn(glassCard, "h-fit p-6")}>
                  <div className="grid h-12 w-12 place-items-center rounded-2xl bg-sky-400/15 text-sky-100">
                    <Search className="h-6 w-6" aria-hidden="true" />
                  </div>
                  <h3 className="mt-5 text-xl font-black">Selecciona un sitio</h3>
                  <p className="mt-2 text-sm leading-6 text-white/65">
                    El mapa mostrará un único punto de referencia a la vez.
                  </p>

                  <label htmlFor="site-selector" className="mt-6 block text-xs font-black uppercase tracking-[0.18em] text-white/50">
                    Punto de estudio
                  </label>
                  <select
                    id="site-selector"
                    value={selectedSiteId}
                    onChange={(event) => setSelectedSiteId(event.target.value)}
                    className="mt-2 h-12 w-full rounded-2xl border border-white/15 bg-slate-950/70 px-4 text-sm font-semibold text-white outline-none ring-sky-400/60 transition focus:ring-2"
                  >
                    <option value="">Vista general del Ecuador</option>
                    {mapSites.map((site) => (
                      <option key={site.id} value={site.id}>
                        {site.name}
                      </option>
                    ))}
                  </select>

                  <div className="mt-6 rounded-2xl border border-white/10 bg-black/10 p-4">
                    {selectedSite ? (
                      <>
                        <p className="text-xs font-black uppercase tracking-[0.18em] text-white/45">Selección actual</p>
                        <h4 className="mt-2 font-bold text-white">{selectedSite.name}</h4>
                        <p className="mt-1 text-xs font-semibold text-sky-200">{selectedSite.category}</p>
                        <p className="mt-3 text-sm leading-6 text-white/65">{selectedSite.description}</p>
                      </>
                    ) : (
                      <p className="text-sm leading-6 text-white/60">
                        Selecciona un sitio para acercar la vista y mostrar su único marcador de referencia.
                      </p>
                    )}
                  </div>
                </aside>

                <div className={cn(glassCard, "overflow-hidden p-2 sm:p-3")}>
                  <div className="relative z-0 h-[560px] overflow-hidden rounded-[1.4rem] border border-white/10">
                    <MapContainer
                      center={[-1.8312, -78.1834]}
                      zoom={6}
                      minZoom={3}
                      scrollWheelZoom
                      className="h-full w-full"
                    >
                      <TileLayer
                        attribution='Tiles &copy; Esri &mdash; Source: Esri, Maxar, Earthstar Geographics, and the GIS User Community'
                        url="https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}"
                      />

                      <FlyToSelected site={selectedSite} />

                      {selectedSite && (
                        <CircleMarker
                          center={selectedSite.position}
                          radius={10}
                          pathOptions={{
                            color: "#ffffff",
                            weight: 3,
                            fillColor: selectedSite.color,
                            fillOpacity: 0.92,
                          }}
                        >
                          <Popup>
                            <div className="max-w-64">
                              <strong>{selectedSite.name}</strong>
                              <br />
                              <span>{selectedSite.description}</span>
                            </div>
                          </Popup>
                        </CircleMarker>
                      )}
                    </MapContainer>
                  </div>
                </div>
              </div>
            </main>
          </TabsContent>

          {/* 4. EVALUACIÓN */}
          <TabsContent value="evaluacion" className="m-0 focus-visible:outline-none">
            <main className="mx-auto max-w-7xl px-4 py-10 sm:px-8 md:py-14">
              <SectionTitle
                icon={ClipboardCheck}
                kicker="Evaluación"
                title="Espacio para evidencias y valoración"
                description="Esta sección queda preparada para integrar cuestionarios, actividades de cierre y entregas de proyectos sin mezclar la evaluación con la teoría de las unidades."
              />

              <div className="grid gap-6 lg:grid-cols-3">
                <div className={cn(glassCard, "p-6")}>
                  <div className="grid h-12 w-12 place-items-center rounded-2xl bg-emerald-400/15 text-emerald-100">
                    <GraduationCap className="h-6 w-6" aria-hidden="true" />
                  </div>
                  <span className="mt-5 inline-flex rounded-full border border-white/10 bg-white/5 px-3 py-1 text-xs font-bold text-white/55">
                    Próximamente
                  </span>
                  <h3 className="mt-4 text-xl font-black">Cuestionario de valoración</h3>
                  <p className="mt-3 text-sm leading-7 text-white/65">
                    Espacio destinado al instrumento para valorar utilidad, usabilidad y pertinencia didáctica de BIOSIG.
                  </p>
                </div>

                <div className={cn(glassCard, "p-6")}>
                  <div className="grid h-12 w-12 place-items-center rounded-2xl bg-sky-400/15 text-sky-100">
                    <Microscope className="h-6 w-6" aria-hidden="true" />
                  </div>
                  <span className="mt-5 inline-flex rounded-full border border-white/10 bg-white/5 px-3 py-1 text-xs font-bold text-white/55">
                    Próximamente
                  </span>
                  <h3 className="mt-4 text-xl font-black">Quiz de unidades</h3>
                  <p className="mt-3 text-sm leading-7 text-white/65">
                    Área preparada para preguntas breves sobre megadiversidad, SNAP, conservación y lectura espacial.
                  </p>
                </div>

                <div className={cn(glassCard, "p-6")}>
                  <div className="grid h-12 w-12 place-items-center rounded-2xl bg-amber-400/15 text-amber-100">
                    <FileText className="h-6 w-6" aria-hidden="true" />
                  </div>
                  <span className="mt-5 inline-flex rounded-full border border-white/10 bg-white/5 px-3 py-1 text-xs font-bold text-white/55">
                    Próximamente
                  </span>
                  <h3 className="mt-4 text-xl font-black">Entrega de proyecto SIG</h3>
                  <p className="mt-3 text-sm leading-7 text-white/65">
                    Espacio para incorporar evidencias, capturas cartográficas, fichas de análisis o enlaces a proyectos desarrollados por los estudiantes.
                  </p>
                </div>
              </div>

              <div className={cn(glassCard, "mt-8 p-6 sm:p-8")}>
                <div className="flex flex-col gap-5 md:flex-row md:items-center md:justify-between">
                  <div>
                    <p className="text-xs font-black uppercase tracking-[0.2em] text-white/45">Estado del módulo</p>
                    <h3 className="mt-2 text-2xl font-black">Listo para conectar el instrumento definitivo</h3>
                    <p className="mt-2 max-w-3xl text-sm leading-7 text-white/65">
                      Cuando el cuestionario esté validado, aquí puede integrarse Microsoft Forms u otro mecanismo institucional de recolección de datos.
                    </p>
                  </div>
                  <div className="grid h-16 w-16 shrink-0 place-items-center rounded-3xl border border-white/10 bg-white/10">
                    <Sparkles className="h-7 w-7 text-amber-200" aria-hidden="true" />
                  </div>
                </div>
              </div>
            </main>
          </TabsContent>

          {/* 5. BIBLIOGRAFÍA */}
          <TabsContent value="bibliografia" className="m-0 focus-visible:outline-none">
            <main className="mx-auto max-w-7xl px-4 py-10 sm:px-8 md:py-14">
              <SectionTitle
                icon={FileText}
                kicker="Bibliografía"
                title="Fuentes académicas de BIOSIG"
                description="Referencias utilizadas para fundamentar la integración educativa de los SIG, el pensamiento espacial y la conservación de la biodiversidad."
              />

              <div className="grid gap-4 lg:grid-cols-2">
                {bibliography.map((item, index) => (
                  <article key={item.url} className={cn(softGlassCard, "p-5 sm:p-6")}>
                    <div className="flex gap-4">
                      <div className="grid h-9 w-9 shrink-0 place-items-center rounded-xl bg-white/10 text-xs font-black text-white/70">
                        {String(index + 1).padStart(2, "0")}
                      </div>
                      <div className="min-w-0">
                        <p className="text-sm leading-7 text-white/75">{item.reference}</p>
                        <a
                          href={item.url}
                          target="_blank"
                          rel="noopener noreferrer"
                          className="mt-3 inline-flex items-center gap-2 text-sm font-bold text-sky-200 transition hover:text-white"
                        >
                          Consultar fuente
                          <ExternalLink className="h-4 w-4" aria-hidden="true" />
                        </a>
                      </div>
                    </div>
                  </article>
                ))}
              </div>

              <div className={cn(glassCard, "mt-8 p-6 sm:p-8")}>
                <div className="flex items-start gap-4">
                  <div className="grid h-11 w-11 shrink-0 place-items-center rounded-2xl bg-white/10">
                    <ShieldCheck className="h-5 w-5 text-emerald-100" aria-hidden="true" />
                  </div>
                  <div>
                    <h3 className="text-lg font-black">Recursos cartográficos utilizados</h3>
                    <p className="mt-2 text-sm leading-7 text-white/65">
                      Google Earth, NASA Worldview, el visor interactivo del MAATE, OpenStreetMap y la capa satelital Esri World Imagery se emplean como recursos de exploración geoespacial dentro de las actividades propuestas.
                    </p>
                  </div>
                </div>
              </div>
            </main>
          </TabsContent>
        </Tabs>

        <footer className="border-t border-white/10 bg-black/10 px-4 py-8 backdrop-blur-xl sm:px-8">
          <div className="mx-auto flex max-w-7xl flex-col gap-2 text-xs text-white/45 md:flex-row md:items-center md:justify-between">
            <p>BIOSIG · Recurso didáctico para la enseñanza de biodiversidad y conservación en el Ecuador.</p>
            <p>Universidad Nacional de Chimborazo · Pedagogía de las Ciencias Experimentales: Química y Biología.</p>
          </div>
        </footer>
      </div>
    </div>
  );
}

export default Index;
