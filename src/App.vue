<script setup lang="ts">
import { onMounted, ref, onUnmounted } from 'vue';
import ShapeGrid from './components/ui/ShapeGrid.vue';
import {
  Carousel,
  CarouselContent,
  CarouselItem,
  CarouselNext,
  CarouselPrevious,
} from '@/components/ui/carousel';
import { Card, CardContent } from '@/components/ui/card';
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar';
import { Input } from '@/components/ui/input';
import { Textarea } from '@/components/ui/textarea';

import caioImage from './assets/CaioCorreia.jpg';
import michaelImage from './assets/MichaelCastro.png';
import allysonImage from './assets/AllysonAlves.png';
import myImageAvatar from './assets/myImage.jpg';

const myImage = myImageAvatar;
const myName = 'Gabriel Lamas';
const myTitle = 'FullStack Developer · Vue.js & C#/.NET';
const aboutMeText = `Desenvolvedor Full Stack com mais de 4 anos de experiência no desenvolvimento de sistemas utilizando C#, .NET, Vue.js e SQL Server.

Iniciei minha trajetória na programação em 2018, durante minha formação técnica no NAVE, onde tive meu primeiro contato com desenvolvimento de software e com o ecossistema Microsoft. Desde então, venho atuando na construção e evolução de aplicações corporativas, APIs, integrações entre sistemas e soluções voltadas para ambientes de produção.

Atualmente trabalho com tecnologias como .NET, C#, Vue.js, Docker, RabbitMQ, SQL Server e Azure, além de aprofundar meus conhecimentos em arquitetura de software através da Pós-Graduação em Arquitetura de Sistemas .NET da FIAP.

Tenho grande interesse por arquitetura de sistemas, boas práticas de desenvolvimento, microsserviços e soluções escaláveis, buscando sempre entregar software de qualidade que gere valor para usuários e negócios.`;

// Animação de entrada ao rolar a página (respeita prefers-reduced-motion)
let revealObserver: IntersectionObserver | null = null;
const vReveal = {
  mounted(el: HTMLElement, binding: { value?: number }) {
    if (typeof window === 'undefined' || !('IntersectionObserver' in window)) return;
    if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) return;
    el.classList.add('reveal');
    el.style.transitionDelay = `${Math.min(binding.value ?? 0, 6) * 80}ms`;
    revealObserver ??= new IntersectionObserver(
      (entries) => {
        for (const entry of entries) {
          if (!entry.isIntersecting) continue;
          entry.target.classList.add('reveal-in');
          revealObserver?.unobserve(entry.target);
        }
      },
      { threshold: 0.12, rootMargin: '0px 0px -40px 0px' },
    );
    revealObserver.observe(el);
  },
};

const isNavVisible = ref(false);
const auroraRef = ref(null);
const activeSection = ref('sobre-mim');
const emailSubject = ref('');
const emailBody = ref('');
const targetEmail = 'GLamas.dev@gmail.com';

const sendEmail = () => {
  const subject = encodeURIComponent(emailSubject.value);
  const body = encodeURIComponent(emailBody.value);
  window.location.href = `mailto:${targetEmail}?subject=${subject}&body=${body}`;
};

const navSections = [
  { id: 'sobre-mim', label: 'Sobre Mim' },
  { id: 'habilidades', label: 'Habilidades' },
  { id: 'formacao', label: 'Formação' },
  { id: 'experiencia', label: 'Experiência' },
  { id: 'projetos', label: 'Projetos' },
  { id: 'recomendacoes', label: 'Recomendações' },
];

const sectionIds = ['sobre-mim', 'habilidades', 'formacao', 'experiencia', 'projetos', 'recomendacoes', 'contato'];

const handleScroll = () => {
  if (auroraRef.value) {
    const auroraHeight = (auroraRef.value as HTMLElement).offsetHeight;
    isNavVisible.value = window.scrollY > auroraHeight * 0.4;
  } else {
    isNavVisible.value = window.scrollY > 100;
  }

  let currentSection: string = sectionIds[0] || '';
  for (const id of sectionIds) {
    const el = document.getElementById(id);
    if (el) {
      const rect = el.getBoundingClientRect();
      if (rect.top <= 120 && rect.bottom > 120) {
        currentSection = id;
        break;
      }
    }
  }
  activeSection.value = currentSection;
};

onMounted(() => {
  document.documentElement.classList.add('dark');
  document.documentElement.classList.remove('light');
  window.addEventListener('scroll', handleScroll);
});

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll);
});

// Stack principal — destaque
const mainStack = [
  { name: '.NET', icon: 'devicon-dotnetcore-plain', theme: 'blue' },
  { name: 'Angular', icon: 'devicon-angularjs-plain', theme: 'red' },
];

// Demais tecnologias — botões coloridos por categoria, tudo na mesma área
const techStack = [
  { name: 'React', icon: 'devicon-react-original', theme: 'sky' },
  { name: 'Vue.js', icon: 'devicon-vuejs-plain', theme: 'emerald' },
  { name: 'AWS', icon: 'devicon-amazonwebservices-plain-wordmark', theme: 'orange' },
  { name: 'Azure', icon: 'devicon-azure-plain', theme: 'sky' },
  { name: 'Grafana', icon: 'devicon-grafana-original', theme: 'amber' },
  { name: 'RabbitMQ', icon: 'devicon-rabbitmq-original', theme: 'orange' },
  { name: 'Apache Kafka', icon: 'devicon-apachekafka-original', theme: 'slate' },
];

// Classes de cor por tema (estáticas p/ o Tailwind detectar)
const themeClasses: Record<string, string> = {
  blue: 'border-blue-500/30 bg-blue-500/10 text-blue-300 hover:border-blue-500/60 hover:bg-blue-500/20 hover:shadow-blue-500/20',
  red: 'border-red-500/30 bg-red-500/10 text-red-300 hover:border-red-500/60 hover:bg-red-500/20 hover:shadow-red-500/20',
  sky: 'border-sky-500/30 bg-sky-500/10 text-sky-300 hover:border-sky-500/60 hover:bg-sky-500/20 hover:shadow-sky-500/20',
  emerald: 'border-emerald-500/30 bg-emerald-500/10 text-emerald-300 hover:border-emerald-500/60 hover:bg-emerald-500/20 hover:shadow-emerald-500/20',
  orange: 'border-orange-500/30 bg-orange-500/10 text-orange-300 hover:border-orange-500/60 hover:bg-orange-500/20 hover:shadow-orange-500/20',
  amber: 'border-amber-500/30 bg-amber-500/10 text-amber-300 hover:border-amber-500/60 hover:bg-amber-500/20 hover:shadow-amber-500/20',
  slate: 'border-slate-400/30 bg-slate-400/10 text-slate-200 hover:border-slate-400/60 hover:bg-slate-400/20 hover:shadow-slate-400/20',
};

// Princípios e práticas como pills
const principles = [
  'Domain-Driven Design (DDD)',
  'Clean Architecture',
  'SOLID',
  'TDD / BDD',
  'CQRS',
  'Event-Driven Architecture',
  'Clean Code',
  'DRY',
  'YAGNI',
  'REST API',
  'Gitflow',
  'CI / CD',
  'Versionamento Semântico',
];

const experiences = [
  {
    id: 1,
    role: 'Desenvolvedor Full Stack',
    company: 'TIPLAN',
    companyLink: 'https://www.tiplan.com.br/',
    type: 'Tempo integral',
    duration: 'out de 2025 — presente',
    location: 'Rio de Janeiro',
    isCurrent: true,
  },
  {
    id: 2,
    role: 'Desenvolvedor Full Stack · .NET / C#',
    company: 'Target Work',
    companyLink: 'https://targetwork.com.br/',
    type: 'Terceirizado',
    duration: 'jul de 2023 — out de 2025 · 2 anos 4 meses',
    location: 'Rio de Janeiro, Brasil · Remota',
    isCurrent: false,
  },
  {
    id: 3,
    role: 'Assistente de TI',
    company: 'GlobalTera',
    companyLink: 'https://globaltera.com.br/',
    type: 'Tempo integral',
    duration: 'jan de 2023 — jul de 2023 · 7 meses',
    location: 'Barra da Tijuca, Rio de Janeiro · Presencial',
    isCurrent: false,
  },
  {
    id: 4,
    role: 'Analista de Qualidade e Suporte',
    company: 'Nuvor',
    companyLink: 'https://nuvor.com.br/',
    type: 'Temporário',
    duration: 'mar de 2023 — mai de 2023 · 3 meses',
    location: 'Rio de Janeiro, Brasil',
    isCurrent: false,
  },
];

const educations = [
  {
    id: 5,
    course: 'Arquitetura de Sistemas .NET',
    type: 'Pós-Graduação',
    institution: 'FIAP',
    duration: '2026 — 2027',
    location: 'Rio de Janeiro',
    upcoming: true,
  },
  {
    id: 4,
    course: 'Análise e Desenvolvimento de Sistemas',
    type: 'Graduação',
    institution: 'Estácio',
    duration: '2023 — 2025',
    location: 'Rio de Janeiro',
    upcoming: false,
  },
  {
    id: 3,
    course: 'C# Avançado / Arquiteto .NET',
    type: 'Formação Profissionalizante',
    institution: 'COTI Informática',
    duration: 'Fevereiro 2023',
    location: 'Online',
    upcoming: false,
  },
  {
    id: 2,
    course: 'C# Web Developer Fullstack',
    type: 'Formação Profissionalizante',
    institution: 'COTI Informática',
    duration: 'Fevereiro 2023',
    location: 'Online',
    upcoming: false,
  },
  {
    id: 1,
    course: 'Programação de Jogos Digitais',
    type: 'Ensino Médio Técnico',
    institution: 'NAVE',
    duration: '2018 — 2020',
    location: 'Rio de Janeiro',
    upcoming: false,
  },
];

// Projetos
type Project = {
  id: number;
  phase: string;
  label: string;
  title: string;
  description: string;
  thumbnail: string;
  tags: string[];
  repoUrl: string;
  videoUrl: string | null;
  videoSoon?: boolean;
  inProgress?: boolean;
  accent: string;
};

const techChallengeProjects: Project[] = [
  {
    id: 1,
    phase: 'Fase 1',
    label: 'Tech Challenge · Pós-Tech FIAP',
    title: 'API REST com .NET 8 e DDD',
    description:
      'Primeira versão da FIAP Cloud Games: API monolítica em camadas inspiradas em DDD, com autenticação JWT, perfis de usuário e administrador, biblioteca de jogos, promoções e 92 testes automatizados.',
    thumbnail: '/projetos/tech-challenge-fase-1.jpg',
    tags: ['.NET 8', 'DDD', 'EF Core', 'JWT', 'OpenTelemetry', 'xUnit'],
    repoUrl: 'https://github.com/LMXSS/TC1',
    videoUrl: 'https://youtu.be/HUkTjjhVugY',
    accent: 'violet',
  },
  {
    id: 2,
    phase: 'Fase 2',
    label: 'Tech Challenge · Pós-Tech FIAP',
    title: 'Do monolito aos microsserviços',
    description:
      'O monolito dividido em 4 microsserviços .NET (usuários, catálogo, pagamentos e notificações) que se comunicam por eventos, com fluxo de compra assíncrono, idempotência e escala com HPA.',
    thumbnail: '/projetos/tech-challenge-fase-2.jpg',
    tags: ['Microsserviços', 'RabbitMQ', 'MassTransit', 'Docker', 'Kubernetes'],
    repoUrl: 'https://github.com/LMXSS/TC2',
    videoUrl: 'https://youtu.be/ozCAqzTyqTI',
    accent: 'orange',
  },
  {
    id: 3,
    phase: 'Fase 3',
    label: 'Tech Challenge · Pós-Tech FIAP',
    title: 'Serverless + API Gateway',
    description:
      'Evolução da arquitetura: notificações migradas para uma função serverless e os microsserviços atrás de um API Gateway, com CQRS, cache e monitoramento.',
    thumbnail: '/projetos/tech-challenge-fase-3.jpg',
    tags: ['Azure Functions', 'Kong', 'CQRS', 'MongoDB', 'Redis', 'Grafana'],
    repoUrl: 'https://github.com/LMXSS/TC3',
    videoUrl: 'https://youtu.be/tXTYzvfqRQ0',
    accent: 'emerald',
  },
  {
    id: 4,
    phase: 'Fase 4',
    label: 'Tech Challenge · Pós-Tech FIAP',
    title: 'Kubernetes na Azure',
    description:
      'A plataforma FIAP Cloud Games publicada na nuvem: microsserviços .NET rodando no Azure Kubernetes Service, imagens no Azure Container Registry e Elasticsearch, MongoDB e Redis no cluster.',
    thumbnail: '/projetos/tech-challenge-fase-4.jpg',
    tags: ['Azure AKS', 'ACR', 'Kubernetes', 'Elasticsearch', 'MongoDB', 'Redis'],
    repoUrl: 'https://github.com/LMXSS/TC4',
    videoUrl: null,
    videoSoon: true,
    inProgress: true,
    accent: 'blue',
  },
];

const studyProjects: Project[] = [
  {
    id: 5,
    phase: 'Performance',
    label: 'Estudo · .NET 8',
    title: 'Regex vs código com BenchmarkDotNet',
    description:
      'Experimento que mede, com BenchmarkDotNet, a diferença de performance entre validar e-mails com expressão regular e com uma validação manual que percorre a string uma única vez.',
    thumbnail: '/projetos/benchmarkdotnet.jpg',
    tags: ['.NET 8', 'C#', 'BenchmarkDotNet', 'Regex'],
    repoUrl: 'https://github.com/LMXSS/BenchmarkDotnet',
    videoUrl: null,
    accent: 'amber',
  },
  {
    id: 6,
    phase: 'Autenticação',
    label: 'Estudo · .NET 10',
    title: 'Identity API com RabbitMQ',
    description:
      'API de autenticação com os Identity API Endpoints do ASP.NET Core (cadastro, login com token, refresh e 2FA) e mensageria assíncrona com MassTransit, em camadas e com teste do consumer.',
    thumbnail: '/projetos/identity.jpg',
    tags: ['.NET 10', 'ASP.NET Identity', 'MassTransit', 'RabbitMQ', 'SQLite', 'xUnit'],
    repoUrl: 'https://github.com/LMXSS/Identity',
    videoUrl: null,
    accent: 'rose',
  },
  {
    id: 7,
    phase: 'Ferramenta',
    label: 'Migração de DI · Python',
    title: 'XMLToBind',
    description:
      'Script que converte a configuração do Ninject em XML para bindings em C#. Usei para migrar a injeção de dependência de um sistema legado sem reescrever cada registro à mão.',
    thumbnail: '/projetos/xmltobind.jpg',
    tags: ['Python', 'XML', 'Ninject', 'C#'],
    repoUrl: 'https://github.com/LMXSS/XMLToBind',
    videoUrl: null,
    accent: 'lime',
  },
];

const projectGroups = [
  {
    id: 'tech-challenge',
    title: 'Tech Challenge · Pós-Tech FIAP',
    subtitle: 'FIAP Cloud Games, evoluída fase a fase: do monolito ao Kubernetes na Azure.',
    grid: 'md:grid-cols-2',
    items: techChallengeProjects,
  },
  {
    id: 'estudos',
    title: 'Estudos e ferramentas',
    subtitle: 'Experimentos de performance, autenticação e utilitários criados no dia a dia.',
    grid: 'md:grid-cols-2 lg:grid-cols-3',
    items: studyProjects,
  },
];

// Cores de destaque por projeto (estáticas p/ o Tailwind detectar)
const projectAccent: Record<string, { badge: string; hover: string }> = {
  violet: { badge: 'bg-violet-500/90 text-white', hover: 'hover:border-violet-500/50 hover:shadow-violet-500/10' },
  orange: { badge: 'bg-orange-500/90 text-white', hover: 'hover:border-orange-500/50 hover:shadow-orange-500/10' },
  emerald: { badge: 'bg-emerald-500/90 text-white', hover: 'hover:border-emerald-500/50 hover:shadow-emerald-500/10' },
  blue: { badge: 'bg-blue-500/90 text-white', hover: 'hover:border-blue-500/50 hover:shadow-blue-500/10' },
  amber: { badge: 'bg-amber-400 text-zinc-950', hover: 'hover:border-amber-400/50 hover:shadow-amber-400/10' },
  rose: { badge: 'bg-rose-500/90 text-white', hover: 'hover:border-rose-500/50 hover:shadow-rose-500/10' },
  lime: { badge: 'bg-lime-400 text-zinc-950', hover: 'hover:border-lime-400/50 hover:shadow-lime-400/10' },
};

const testimonials = [
  {
    id: 1,
    name: 'Caio Correia',
    title: 'Scrum Master',
    company: 'Target Work',
    image: caioImage,
    linkedIn: 'https://www.linkedin.com/in/caiocesarcorreia/',
    message: 'No meu tempo supervisionando o Gabriel, ele se mostrou uma pessoa capaz de assumir desenvolvimentos e projetos complexos para serem desenvolvidos, mesmo com o pouco tempo de experiência que ele tinha na área. Além disso, ele é extremamente comunicativo, o que tornava mais fácil transmitir as tarefas para ele de modo que entendesse exatamente o que estava sendo pedido.',
  },
  {
    id: 2,
    name: 'Michael Castro',
    title: 'Gestor',
    company: 'GlobalTera',
    image: michaelImage,
    linkedIn: 'https://www.linkedin.com/in/michael-castro-899847289/',
    message: 'Gabriel foi realmente um destaque no nosso time de suporte, entrou como JR mas chegou assumindo diversas responsabilidades. Sua dedicação incansável e habilidades excepcionais foram fundamentais para a eficiência da equipe. Tive o prazer e a honra de trabalhar com esse jovem que apesar da idade tem uma cabeça pensante incrível.',
  },
  {
    id: 3,
    name: 'Allyson Alves',
    title: 'Desenvolvedor FullStack',
    company: 'Target Work',
    image: allysonImage,
    linkedIn: 'https://www.linkedin.com/in/allyson-alves/',
    message: 'Gabriel é um excelente desenvolvedor .NET C# com quem tive a oportunidade de trabalhar. Sua habilidade técnica é impressionante, e sua abordagem prática para resolver desafios torna-o um membro valioso da equipe. O que diferencia Gabriel não é apenas seu conhecimento técnico, mas sua postura proativa em busca constante de aprendizado e crescimento profissional.',
  },
];
</script>

<template>
  <!-- ─── NAV ─────────────────────────────────────── -->
  <nav
    :class="isNavVisible
      ? 'translate-y-0 opacity-100'
      : '-translate-y-full opacity-0 pointer-events-none'"
    class="fixed top-0 left-0 w-full z-[100] backdrop-blur-xl bg-black/50 border-b border-indigo-500/15 transition-all duration-500 ease-in-out"
  >
    <div class="max-w-6xl mx-auto px-6 h-16 flex items-center justify-between">
      <a href="#sobre-mim" class="dev-logo font-black text-xl tracking-tight select-none">
        <span class="glyph-braces bg-gradient-to-r from-indigo-400 to-violet-400 bg-clip-text text-transparent">&#123; &#125;</span>
        <span class="glyph-tag bg-gradient-to-r from-indigo-400 to-violet-400 bg-clip-text text-transparent">&lt;/&gt;</span>
      </a>
      <div class="hidden sm:flex items-center gap-1">
        <a
          v-for="s in navSections"
          :key="s.id"
          :href="`#${s.id}`"
          :class="activeSection === s.id
            ? 'text-indigo-400 bg-indigo-500/10'
            : 'text-slate-400 hover:text-slate-100 hover:bg-white/5'"
          class="px-3 py-1.5 text-sm font-medium rounded-lg transition-all duration-200"
        >
          {{ s.label }}
        </a>
        <a href="#contato" class="ml-2">
          <button class="px-4 py-1.5 text-sm font-semibold rounded-lg bg-indigo-600 hover:bg-indigo-500 text-white transition-colors duration-200 shadow-lg shadow-indigo-500/20">
            Contato
          </button>
        </a>
      </div>
    </div>
  </nav>

  <div class="min-h-screen">

    <!-- ─── HERO ──────────────────────────────────── -->
    <section
      ref="auroraRef"
      class="relative flex h-screen flex-col items-center justify-center overflow-hidden bg-background"
    >
      <ShapeGrid
        class="absolute inset-0 z-0"
        shape="hexagon"
        direction="diagonal"
        :speed="0.5"
        :square-size="40"
        border-color="rgba(99, 102, 241, 0.22)"
        hover-fill-color="rgba(129, 140, 248, 0.45)"
        :hover-trail-amount="5"
        fade-color="#05060f"
      />
      <div class="flex flex-col items-center text-center gap-5 relative z-20 px-6">
        <h1 class="text-5xl sm:text-7xl md:text-8xl font-black tracking-tight text-white leading-none">
          Gabriel<br>
          <span class="bg-gradient-to-r from-indigo-400 via-violet-400 to-indigo-300 bg-clip-text text-transparent">
            Lamas
          </span>
        </h1>

        <p class="text-base sm:text-xl text-slate-300 font-light max-w-md">
          FullStack Developer · Vue.js &amp; C#/.NET
        </p>

        <div class="flex flex-wrap gap-3 justify-center mt-1">
          <a href="#projetos">
            <button class="px-6 py-2.5 rounded-xl bg-indigo-600 hover:bg-indigo-500 text-white font-semibold text-sm transition-all duration-200 hover:scale-105 shadow-lg shadow-indigo-500/30 flex items-center gap-2">
              <i class="devicon-github-original text-base"></i>
              Ver Projetos
            </button>
          </a>
          <a href="#contato">
            <button class="px-6 py-2.5 rounded-xl border border-white/20 hover:border-white/40 text-white font-semibold text-sm transition-all duration-200 hover:scale-105 bg-white/5 hover:bg-white/10">
              Entrar em Contato
            </button>
          </a>
        </div>

        <div class="flex items-center gap-2 px-3 py-1 rounded-full border border-emerald-500/30 bg-emerald-500/10 text-emerald-400 text-xs font-medium mt-1">
          <span class="w-1.5 h-1.5 rounded-full bg-emerald-400 animate-pulse"></span>
          Disponível para novos projetos
        </div>
      </div>
    </section>

    <!-- ─── SOBRE MIM ──────────────────────────────── -->
    <section id="sobre-mim" class="py-20 md:py-32 bg-background">
      <div class="max-w-5xl mx-auto px-6">
        <div class="flex flex-col md:flex-row gap-12 md:gap-16 items-center">

          <div class="flex-shrink-0 flex flex-col items-center gap-5">
            <div class="relative">
              <div class="absolute -inset-[3px] rounded-full bg-gradient-to-br from-indigo-500 via-violet-500 to-indigo-400 opacity-80 blur-[2px]"></div>
              <div class="relative w-44 h-44 md:w-56 md:h-56 rounded-full p-[3px] bg-gradient-to-br from-indigo-500 via-violet-500 to-indigo-400">
                <img :src="myImage" alt="Gabriel Lamas" class="w-full h-full rounded-full object-cover" />
              </div>
              <div class="absolute inset-0 rounded-full bg-indigo-500/15 blur-2xl -z-10 scale-125"></div>
            </div>
            <div class="flex gap-6 text-center">
              <div>
                <p class="text-2xl font-bold text-indigo-400">3+</p>
                <p class="text-xs text-muted-foreground">Anos de exp.</p>
              </div>
              <div class="w-px bg-border"></div>
              <div>
                <p class="text-2xl font-bold text-indigo-400">15+</p>
                <p class="text-xs text-muted-foreground">Tecnologias</p>
              </div>
            </div>
          </div>

          <div class="flex-1">
            <p class="section-label mb-3">01 — Sobre Mim</p>
            <h2 class="text-4xl md:text-5xl font-black mb-2 text-foreground leading-tight">{{ myName }}</h2>
            <p class="text-lg text-indigo-400 font-medium mb-6">{{ myTitle }}</p>
            <p class="text-muted-foreground leading-relaxed text-base whitespace-pre-line">{{ aboutMeText }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- ─── HABILIDADES ────────────────────────────── -->
    <section id="habilidades" class="py-20 md:py-32 bg-muted">
      <div class="max-w-6xl mx-auto px-6">
        <div class="text-center mb-16">
          <p class="section-label mb-3">02 — Stack</p>
          <h2 class="text-4xl font-black text-foreground">Habilidades Técnicas</h2>
          <p class="text-muted-foreground mt-3 text-sm max-w-md mx-auto">
            Foco em <span class="text-blue-300 font-semibold">.NET</span> e
            <span class="text-red-300 font-semibold">Angular</span> — do back-end ao front-end.
          </p>
        </div>

        <!-- Stack — botões coloridos por tecnologia, tudo na mesma área -->
        <div class="max-w-3xl mx-auto flex flex-wrap items-center justify-center gap-3">
          <!-- Stack principal (destaque) -->
          <button
            v-for="tech in mainStack"
            :key="tech.name"
            type="button"
            :class="[
              'flex items-center gap-2.5 px-5 py-3 rounded-xl border font-bold text-base transition-all duration-300 hover:-translate-y-1 hover:shadow-lg cursor-default',
              themeClasses[tech.theme],
            ]"
          >
            <i :class="[tech.icon, 'text-2xl']"></i>
            <span>{{ tech.name }}</span>
          </button>

          <!-- Demais tecnologias -->
          <button
            v-for="tech in techStack"
            :key="tech.name"
            type="button"
            :class="[
              'flex items-center gap-2 px-4 py-2.5 rounded-xl border font-medium text-sm transition-all duration-300 hover:-translate-y-1 hover:shadow-lg cursor-default',
              themeClasses[tech.theme],
            ]"
          >
            <i :class="[tech.icon, 'text-xl']"></i>
            <span>{{ tech.name }}</span>
          </button>
        </div>

        <!-- Princípios & Práticas -->
        <div class="mt-14">
          <div class="flex items-center gap-4 mb-6">
            <span class="h-px flex-1 bg-border"></span>
            <h3 class="text-xs font-semibold text-muted-foreground px-3 whitespace-nowrap tracking-wide uppercase">Arquitetura &amp; Práticas</h3>
            <span class="h-px flex-1 bg-border"></span>
          </div>
          <div class="flex flex-wrap gap-2 justify-center">
            <span
              v-for="p in principles"
              :key="p"
              class="px-3 py-1.5 rounded-full text-xs font-medium border border-indigo-500/25 bg-indigo-500/8 text-indigo-300 hover:border-indigo-500/50 hover:bg-indigo-500/15 transition-all duration-200 cursor-default"
            >
              {{ p }}
            </span>
          </div>
        </div>
      </div>
    </section>

    <!-- ─── FORMAÇÃO ───────────────────────────────── -->
    <section id="formacao" class="py-20 md:py-32 bg-background">
      <div class="max-w-3xl mx-auto px-6">
        <div class="text-center mb-16">
          <p class="section-label mb-3">03 — Educação</p>
          <h2 class="text-4xl font-black text-foreground">Formação</h2>
        </div>

        <!-- Timeline vertical -->
        <ol class="relative">
          <span aria-hidden="true" class="absolute left-3 -translate-x-1/2 top-3 bottom-3 w-px bg-gradient-to-b from-indigo-500/70 via-indigo-500/30 to-indigo-500/5"></span>

          <li
            v-for="(edu, index) in educations"
            :key="edu.id"
            v-reveal="index"
            class="relative pl-10 pb-5 last:pb-0"
          >
            <span
              aria-hidden="true"
              :class="edu.upcoming
                ? 'w-3.5 h-3.5 bg-violet-400 shadow-[0_0_14px_rgba(167,139,250,0.7)]'
                : 'w-3 h-3 bg-card border-2 border-indigo-400/70'"
              class="absolute left-3 top-[25px] -translate-x-1/2 -translate-y-1/2 rounded-full ring-4 ring-background z-10"
            ></span>

            <div class="group p-5 rounded-2xl border border-border bg-card hover:border-indigo-500/40 transition-all duration-300 hover:-translate-y-0.5 hover:shadow-xl hover:shadow-indigo-500/5">
              <div class="flex flex-wrap items-center justify-between gap-2 mb-2">
                <div class="flex flex-wrap items-center gap-2">
                  <span class="text-xs font-semibold px-2.5 py-0.5 rounded-full bg-indigo-500/10 text-indigo-300 border border-indigo-500/20">
                    {{ edu.type }}
                  </span>
                  <span v-if="edu.upcoming" class="text-xs font-semibold px-2.5 py-0.5 rounded-full bg-violet-500/10 text-violet-300 border border-violet-500/20">
                    Em andamento
                  </span>
                </div>
                <span class="text-xs font-mono text-muted-foreground tabular-nums">{{ edu.duration }}</span>
              </div>
              <h3 class="text-base font-bold text-foreground mb-1 group-hover:text-indigo-300 transition-colors leading-snug">
                {{ edu.course }}
              </h3>
              <p class="text-sm text-muted-foreground">{{ edu.institution }} · {{ edu.location }}</p>
            </div>
          </li>
        </ol>
      </div>
    </section>

    <!-- ─── EXPERIÊNCIA ────────────────────────────── -->
    <section id="experiencia" class="py-20 md:py-32 bg-muted">
      <div class="max-w-3xl mx-auto px-6">
        <div class="text-center mb-16">
          <p class="section-label mb-3">04 — Carreira</p>
          <h2 class="text-4xl font-black text-foreground">Experiência Profissional</h2>
        </div>

        <ol class="relative">
          <span aria-hidden="true" class="absolute left-3 -translate-x-1/2 top-3 bottom-3 w-px bg-gradient-to-b from-indigo-500/70 via-indigo-500/30 to-indigo-500/5"></span>

          <li
            v-for="(exp, index) in experiences"
            :key="exp.id"
            v-reveal="index"
            class="relative pl-10 pb-5 last:pb-0"
          >
            <span
              aria-hidden="true"
              :class="exp.isCurrent
                ? 'w-3.5 h-3.5 bg-indigo-400 shadow-[0_0_14px_rgba(129,140,248,0.8)]'
                : 'w-3 h-3 bg-card border-2 border-indigo-400/50'"
              class="absolute left-3 top-[31px] -translate-x-1/2 -translate-y-1/2 rounded-full ring-4 ring-muted z-10"
            ></span>

            <a :href="exp.companyLink" target="_blank" rel="noopener noreferrer" class="block group">
              <div class="p-5 rounded-2xl border border-border bg-card hover:border-indigo-500/40 transition-all duration-300 hover:-translate-y-0.5 hover:shadow-xl hover:shadow-indigo-500/5">
                <div class="flex flex-wrap items-start justify-between gap-2 mb-2">
                  <h3 class="text-base font-bold text-foreground group-hover:text-indigo-300 transition-colors leading-snug">
                    {{ exp.role }}
                  </h3>
                  <span
                    v-if="exp.isCurrent"
                    class="text-xs px-2.5 py-0.5 rounded-full bg-emerald-500/10 text-emerald-400 border border-emerald-500/25 font-semibold"
                  >
                    Atual
                  </span>
                </div>
                <div class="flex flex-wrap items-center gap-2 text-sm mb-2">
                  <span class="font-semibold text-indigo-400">{{ exp.company }}</span>
                  <span class="text-muted-foreground/50">·</span>
                  <span class="text-muted-foreground">{{ exp.type }}</span>
                </div>
                <div class="flex flex-wrap items-center gap-x-4 gap-y-1 text-xs text-muted-foreground">
                  <span class="flex items-center gap-1.5">
                    <svg class="w-3.5 h-3.5 flex-shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"/>
                    </svg>
                    {{ exp.duration }}
                  </span>
                  <span class="flex items-center gap-1.5">
                    <svg class="w-3.5 h-3.5 flex-shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"/>
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"/>
                    </svg>
                    {{ exp.location }}
                  </span>
                </div>
              </div>
            </a>
          </li>
        </ol>
      </div>
    </section>

    <!-- ─── PROJETOS ───────────────────────────────── -->
    <section id="projetos" class="py-20 md:py-32 bg-background">
      <div class="max-w-6xl mx-auto px-6">
        <div class="text-center mb-16">
          <p class="section-label mb-3">05 — Projetos</p>
          <h2 class="text-4xl font-black text-foreground">Projetos</h2>
          <p class="text-muted-foreground mt-3 text-sm max-w-xl mx-auto">
            Do Tech Challenge da
            <a href="https://postech.fiap.com.br/" target="_blank" rel="noopener noreferrer" class="text-indigo-400 hover:text-indigo-300 underline underline-offset-4">Pós-Tech FIAP</a>
            a estudos e ferramentas que uso no trabalho.
          </p>
        </div>

        <div class="flex flex-col gap-16">
        <div v-for="group in projectGroups" :key="group.id">
          <div class="flex flex-col sm:flex-row sm:items-end sm:justify-between gap-1 mb-6">
            <h3 class="text-xl font-bold text-foreground">{{ group.title }}</h3>
            <p class="text-sm text-muted-foreground">{{ group.subtitle }}</p>
          </div>

        <div :class="['grid grid-cols-1 gap-6', group.grid]">
          <article
            v-for="(project, index) in group.items"
            :key="project.id"
            v-reveal="index"
            :class="['group flex flex-col rounded-2xl border border-border bg-card overflow-hidden transition-all duration-300 hover:-translate-y-1 hover:shadow-xl', projectAccent[project.accent]?.hover]"
          >
            <!-- Thumbnail -->
            <a
              :href="project.videoUrl ?? project.repoUrl"
              target="_blank"
              rel="noopener noreferrer"
              class="relative block aspect-video overflow-hidden bg-muted"
              :aria-label="project.videoUrl ? `Assistir vídeo: ${project.title}` : `Ver repositório: ${project.title}`"
            >
              <img
                :src="project.thumbnail"
                :alt="`Thumbnail do projeto ${project.title}`"
                loading="lazy"
                class="w-full h-full object-cover transition-transform duration-500 group-hover:scale-105"
              />
              <div
                v-if="project.videoUrl"
                class="absolute inset-0 flex items-center justify-center bg-black/0 group-hover:bg-black/40 transition-colors duration-300"
              >
                <span class="flex items-center justify-center w-16 h-16 rounded-full bg-red-600 text-white shadow-xl opacity-0 scale-75 group-hover:opacity-100 group-hover:scale-100 transition-all duration-300">
                  <svg class="w-7 h-7 ml-1" fill="currentColor" viewBox="0 0 24 24" aria-hidden="true"><path d="M8 5v14l11-7z" /></svg>
                </span>
              </div>
              <span
                v-if="project.inProgress"
                class="absolute bottom-3 right-3 flex items-center gap-1.5 px-2.5 py-1 rounded-full text-xs font-semibold bg-black/70 text-amber-300 border border-amber-400/30 backdrop-blur"
              >
                <span class="w-1.5 h-1.5 rounded-full bg-amber-400 animate-pulse"></span>
                Em andamento
              </span>
            </a>

            <!-- Conteúdo -->
            <div class="flex flex-col flex-1 p-6">
              <div class="flex items-center gap-2 mb-3">
                <span :class="['text-xs font-bold px-2.5 py-0.5 rounded-full', projectAccent[project.accent]?.badge]">
                  {{ project.phase }}
                </span>
                <span class="text-xs text-muted-foreground">{{ project.label }}</span>
              </div>

              <h4 class="text-xl font-bold text-foreground mb-2 leading-snug">{{ project.title }}</h4>
              <p class="text-sm text-muted-foreground leading-relaxed mb-4 flex-1">{{ project.description }}</p>

              <div class="flex flex-wrap gap-1.5 mb-5">
                <span
                  v-for="tag in project.tags"
                  :key="tag"
                  class="px-2.5 py-1 rounded-md text-xs font-medium bg-muted text-slate-300 border border-border"
                >
                  {{ tag }}
                </span>
              </div>

              <div class="flex flex-wrap gap-3 mt-auto">
                <a
                  v-if="project.videoUrl"
                  :href="project.videoUrl"
                  target="_blank"
                  rel="noopener noreferrer"
                  class="flex items-center gap-2 px-4 py-2 rounded-xl bg-red-600 hover:bg-red-500 text-white font-semibold text-sm transition-all duration-200 hover:scale-[1.03] shadow-lg shadow-red-500/20"
                >
                  <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24" aria-hidden="true"><path d="M8 5v14l11-7z" /></svg>
                  Assistir no YouTube
                </a>
                <span
                  v-else-if="project.videoSoon"
                  class="flex items-center gap-2 px-4 py-2 rounded-xl border border-border text-muted-foreground text-sm font-medium cursor-default"
                >
                  Vídeo em breve
                </span>
                <a
                  :href="project.repoUrl"
                  target="_blank"
                  rel="noopener noreferrer"
                  class="flex items-center gap-2 px-4 py-2 rounded-xl bg-zinc-800 hover:bg-zinc-700 text-white font-semibold text-sm transition-all duration-200 hover:scale-[1.03]"
                >
                  <i class="devicon-github-original text-base"></i>
                  Código
                </a>
              </div>
            </div>
          </article>
        </div>
        </div>
        </div>
      </div>
    </section>

    <!-- ─── RECOMENDAÇÕES ──────────────────────────── -->
    <section id="recomendacoes" class="py-20 md:py-32 bg-muted">
      <div class="max-w-4xl mx-auto px-6">
        <div class="text-center mb-16">
          <p class="section-label mb-3">06 — Depoimentos</p>
          <h2 class="text-4xl font-black text-foreground">Recomendações</h2>
        </div>

        <Carousel :opts="{ align: 'center', loop: true }" class="w-full">
          <CarouselContent class="-ml-4 items-stretch">
            <CarouselItem
              v-for="testimonial in testimonials"
              :key="testimonial.id"
              class="basis-full sm:basis-4/5 pl-4"
            >
              <a :href="testimonial.linkedIn" target="_blank" rel="noopener noreferrer" class="block h-full group">
                <Card class="h-full border-border bg-card group-hover:border-indigo-500/40 transition-all duration-300 group-hover:-translate-y-0.5 group-hover:shadow-xl group-hover:shadow-indigo-500/5">
                  <CardContent class="flex flex-col p-7 h-full">
                    <svg class="w-8 h-8 text-indigo-500/40 mb-4 flex-shrink-0" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M14.017 21v-7.391c0-5.704 3.731-9.57 8.983-10.609l.995 2.151c-2.432.917-3.995 3.638-3.995 5.849h4v10h-9.983zm-14.017 0v-7.391c0-5.704 3.748-9.57 9-10.609l.996 2.151c-2.433.917-3.996 3.638-3.996 5.849h3.983v10h-9.983z"/>
                    </svg>
                    <p class="text-muted-foreground leading-relaxed text-sm md:text-base flex-grow mb-6 italic">
                      "{{ testimonial.message }}"
                    </p>
                    <div class="flex items-center gap-4 pt-4 border-t border-border">
                      <Avatar class="h-12 w-12 ring-2 ring-indigo-500/30">
                        <AvatarImage :src="testimonial.image" :alt="testimonial.name" />
                        <AvatarFallback class="bg-indigo-500/20 text-indigo-400 font-bold">
                          {{ testimonial.name.charAt(0) }}
                        </AvatarFallback>
                      </Avatar>
                      <div class="flex-1 min-w-0">
                        <p class="font-bold text-foreground text-sm group-hover:text-indigo-300 transition-colors">{{ testimonial.name }}</p>
                        <p class="text-xs text-muted-foreground">
                          {{ testimonial.title }}
                          <span class="text-indigo-400 font-medium">· {{ testimonial.company }}</span>
                        </p>
                      </div>
                      <i class="devicon-linkedin-plain text-blue-400 text-xl opacity-40 group-hover:opacity-100 transition-opacity flex-shrink-0"></i>
                    </div>
                  </CardContent>
                </Card>
              </a>
            </CarouselItem>
          </CarouselContent>
          <CarouselPrevious class="border-border bg-card hover:border-indigo-500/50 hover:bg-indigo-500/10 text-foreground" />
          <CarouselNext class="border-border bg-card hover:border-indigo-500/50 hover:bg-indigo-500/10 text-foreground" />
        </Carousel>
      </div>
    </section>

    <!-- ─── CONTATO ────────────────────────────────── -->
    <section id="contato" class="py-20 md:py-32 bg-background">
      <div class="max-w-5xl mx-auto px-6">
        <div class="text-center mb-16">
          <p class="section-label mb-3">07 — Contato</p>
          <h2 class="text-4xl font-black text-foreground">Currículo &amp; Contato</h2>
          <p class="text-muted-foreground mt-3 max-w-sm mx-auto text-sm">
            Aberto a oportunidades, freelas e projetos interessantes.
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-5 gap-8 items-start">

          <!-- Sidebar -->
          <div class="md:col-span-2 flex flex-col gap-4">
            <div class="p-5 rounded-2xl border border-border bg-card">
              <h3 class="text-sm font-semibold text-foreground mb-4">Documentos &amp; Contatos</h3>
              <div class="flex flex-col gap-3">
                <a href="/Gabriel%20Lamas%20-%20CV.pdf" download="Gabriel Lamas - Curriculo.pdf" class="flex items-center gap-3 px-4 py-3 rounded-xl bg-indigo-600 hover:bg-indigo-500 text-white font-semibold text-sm transition-all duration-200 hover:scale-[1.02] shadow-lg shadow-indigo-500/20">
                  <svg class="w-4 h-4 flex-shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/>
                  </svg>
                  Baixar Currículo PDF
                </a>
                <a href="https://wa.me/5521964802568" target="_blank" rel="noopener noreferrer" class="flex items-center gap-3 px-4 py-3 rounded-xl bg-green-600 hover:bg-green-500 text-white font-semibold text-sm transition-all duration-200 hover:scale-[1.02] shadow-lg shadow-green-500/20">
                  <svg class="w-5 h-5 flex-shrink-0" fill="currentColor" viewBox="0 0 24 24" aria-hidden="true">
                    <path d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.587-5.946.003-6.556 5.338-11.891 11.893-11.891 3.181.001 6.167 1.24 8.413 3.488 2.245 2.248 3.481 5.236 3.48 8.414-.003 6.557-5.338 11.892-11.893 11.892-1.99-.001-3.951-.5-5.688-1.448l-6.305 1.654zm6.597-3.807c1.676.995 3.276 1.591 5.392 1.592 5.448 0 9.886-4.434 9.889-9.885.002-5.462-4.415-9.89-9.881-9.892-5.452 0-9.887 4.434-9.889 9.884-.001 2.225.651 3.891 1.746 5.634l-.999 3.648 3.742-.981zm11.387-5.464c-.074-.124-.272-.198-.57-.347-.297-.149-1.758-.868-2.031-.967-.272-.099-.47-.149-.669.149-.198.297-.768.967-.941 1.165-.173.198-.347.223-.644.074-.297-.149-1.255-.462-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.297-.347.446-.521.151-.172.2-.296.3-.495.099-.198.05-.372-.025-.521-.075-.148-.669-1.611-.916-2.206-.242-.579-.487-.501-.669-.51l-.57-.01c-.198 0-.52.074-.792.372s-1.04 1.016-1.04 2.479 1.065 2.876 1.213 3.074c.149.198 2.095 3.2 5.076 4.487.709.306 1.263.489 1.694.626.712.226 1.36.194 1.872.118.571-.085 1.758-.719 2.006-1.413.248-.695.248-1.29.173-1.414z"/>
                  </svg>
                  WhatsApp
                </a>
                <a href="https://github.com/LMXSS" target="_blank" rel="noopener noreferrer" class="flex items-center gap-3 px-4 py-3 rounded-xl bg-zinc-800 hover:bg-zinc-700 text-white font-semibold text-sm transition-all duration-200 hover:scale-[1.02] shadow-lg shadow-black/30">
                  <i class="devicon-github-original text-lg flex-shrink-0"></i>
                  GitHub
                </a>
                <a href="https://www.linkedin.com/in/gabriel-lamas-dev/" target="_blank" rel="noopener noreferrer" class="flex items-center gap-3 px-4 py-3 rounded-xl border border-border hover:border-indigo-500/50 text-foreground text-sm font-medium transition-all duration-200 hover:scale-[1.02] hover:bg-indigo-500/5">
                  <i class="devicon-linkedin-plain text-blue-400 text-lg flex-shrink-0"></i>
                  LinkedIn
                </a>
              </div>
            </div>
          </div>

          <!-- Form -->
          <Card class="md:col-span-3 border-border bg-card">
            <CardContent class="p-7">
              <h3 class="text-lg font-bold text-foreground mb-5">Enviar Mensagem Direta</h3>
              <form @submit.prevent="sendEmail" class="space-y-4">
                <div class="space-y-1.5">
                  <label for="subject" class="text-xs font-semibold text-muted-foreground uppercase tracking-wide">Assunto</label>
                  <Input
                    id="subject"
                    v-model="emailSubject"
                    type="text"
                    placeholder="Ex: Proposta de Vaga FullStack"
                    required
                    class="bg-muted border-border focus:border-indigo-500/50 rounded-xl"
                  />
                </div>
                <div class="space-y-1.5">
                  <label for="body" class="text-xs font-semibold text-muted-foreground uppercase tracking-wide">Mensagem</label>
                  <Textarea
                    id="body"
                    v-model="emailBody"
                    placeholder="Sua mensagem detalhada aqui..."
                    rows="6"
                    required
                    class="bg-muted border-border focus:border-indigo-500/50 rounded-xl resize-none"
                  />
                </div>
                <button
                  type="submit"
                  class="w-full py-3 rounded-xl bg-indigo-600 hover:bg-indigo-500 text-white font-semibold text-sm transition-all duration-200 hover:scale-[1.01] shadow-lg shadow-indigo-500/20 flex items-center justify-center gap-2"
                >
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"/>
                  </svg>
                  Entre em contato por E-mail
                </button>
              </form>
            </CardContent>
          </Card>
        </div>
      </div>
    </section>
  </div>

  <!-- ─── FOOTER ─────────────────────────────────── -->
  <footer class="w-full bg-background border-t border-border py-8">
    <div class="max-w-5xl mx-auto px-6 flex flex-col sm:flex-row items-center justify-between gap-4">
      <div class="flex items-center gap-3">
        <span class="dev-logo font-black text-lg">
          <span class="glyph-braces bg-gradient-to-r from-indigo-400 to-violet-400 bg-clip-text text-transparent">&#123; &#125;</span>
          <span class="glyph-tag bg-gradient-to-r from-indigo-400 to-violet-400 bg-clip-text text-transparent">&lt;/&gt;</span>
        </span>
        <span class="text-sm text-muted-foreground">Desenvolvido por Gabriel Lamas · {{ new Date().getFullYear() }}</span>
      </div>
      <div class="flex items-center gap-3">
        <a href="https://github.com/LMXSS" target="_blank" class="text-muted-foreground hover:text-indigo-400 transition-colors text-xl">
          <i class="devicon-github-original"></i>
        </a>
        <a href="https://www.linkedin.com/in/gabriel-lamas-dev/" target="_blank" class="text-muted-foreground hover:text-indigo-400 transition-colors text-xl">
          <i class="devicon-linkedin-plain"></i>
        </a>
      </div>
    </div>
  </footer>
</template>
