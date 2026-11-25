<script setup lang="ts">
import { onMounted, ref, onUnmounted } from 'vue';
import DotField from './components/ui/DotField.vue';
import TechText from './components/ui/TechText.vue';
import { Globe } from 'lucide-vue-next';
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
import vagnerImage from './assets/VagnerHerdi.jpg';
import myImageAvatar from './assets/myImage.jpg';

const myImage = myImageAvatar;
const myName = 'Gabriel Lamas';
const myTitle = 'Desenvolvedor Full Stack .NET';
const myTagline = [
  'Desenvolvo APIs e sistemas distribuídos em C#/.NET, com front-ends em Vue, React e Angular.',
  'Pós-graduando em Arquitetura de Sistemas .NET pela FIAP.',
];
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

// ─── Habilidades ──────────────────────────────────────────
type SkillGroup = { title: string; items: string[] };

const skillGroups: SkillGroup[] = [
  { title: 'Back-end', items: ['C#', '.NET 6+', '.NET Framework 4.5.2', 'ASP.NET Core', 'Entity Framework Core', 'Dapper', 'Python', 'Ruby'] },
  { title: 'Front-end', items: ['Angular', 'React', 'Vue.js', 'TypeScript', 'JavaScript', 'Blazor'] },
  { title: 'Dados', items: ['SQL Server', 'PostgreSQL', 'MongoDB', 'Redis'] },
  { title: 'Mensageria & APIs', items: ['RabbitMQ', 'Apache Kafka', 'SignalR', 'Azure API Management', 'Kong'] },
  { title: 'Cloud & DevOps', items: ['Terraform', 'Azure', 'AWS', 'Docker', 'Kubernetes', 'GitHub Actions'] },
  { title: 'Observabilidade', items: ['Grafana', 'New Relic', 'Datadog', 'Zabbix'] },
];

const practiceGroups: SkillGroup[] = [
  { title: 'Arquitetura', items: ['Microsserviços', 'DDD', 'Clean Architecture', 'CQRS', 'Event-Driven', 'Serverless'] },
  { title: 'Práticas', items: ['SOLID', 'TDD', 'CI/CD', 'Infraestrutura como código', 'Observabilidade'] },
];

// Contagem real de tecnologias para o destaque do "Sobre mim"
const techCount = new Set(skillGroups.flatMap((g) => g.items)).size;
const techCountLabel = `${Math.floor(techCount / 5) * 5}+`;

type Experience = {
  id: number; role: string; company: string; companyLink: string; type: string;
  /** 'AAAA-MM' */
  start: string;
  /** 'AAAA-MM'; null = emprego atual */
  end: string | null;
  location: string; summary?: string; stack?: string[];
};

const experiences: Experience[] = [
  {
    id: 1,
    role: 'Desenvolvedor Full Stack',
    company: 'TIPLAN',
    companyLink: 'https://www.tiplan.com.br/',
    type: 'Tempo integral',
    start: '2025-10',
    end: null,
    location: 'Rio de Janeiro',
    summary:
      'Sistema tributário multi-tenant usado por cinco prefeituras. Atuo no cálculo de IPTU, na declaração de ITBI e na integração com o webservice do TJ-RJ para petições intercorrentes; participei da migração do sistema para .NET 10.',
    stack: ['.NET', 'ASP.NET MVC', 'Entity Framework', 'SQL Server', 'Vue', 'Hangfire', 'Grafana', 'New Relic'],
  },
  {
    id: 2,
    role: 'Desenvolvedor Full Stack · .NET / C#',
    company: 'Target Work',
    companyLink: 'https://targetwork.com.br/',
    type: 'Terceirizado',
    start: '2023-07',
    end: '2025-10',
    location: 'Rio de Janeiro, Brasil · Remota',
    summary:
      'Desenvolvimento e manutenção de aplicações .NET para clientes da consultoria, incluindo a migração de front-ends de Angular para Vue.js.',
    stack: ['C#', '.NET', 'SQL Server', 'Angular', 'Vue.js'],
  },
  {
    id: 3,
    role: 'Assistente de TI',
    company: 'GlobalTera',
    companyLink: 'https://globaltera.com.br/',
    type: 'Tempo integral',
    start: '2023-01',
    end: '2023-07',
    location: 'Barra da Tijuca, Rio de Janeiro · Presencial',
  },
  {
    id: 4,
    role: 'Analista de Qualidade e Suporte',
    company: 'Nuvor',
    companyLink: 'https://nuvor.com.br/',
    type: 'Temporário',
    start: '2023-03',
    end: '2023-05',
    location: 'Rio de Janeiro, Brasil',
  },
];

const MONTHS = ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho', 'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'];

const parseMonth = (ym: string) => {
  const [y, m] = ym.split('-').map(Number);
  return { y: y ?? 0, m: m ?? 1 };
};

// 'AAAA-MM' -> 'Julho 2023'
const formatMonth = (ym: string) => {
  const { y, m } = parseMonth(ym);
  return `${MONTHS[m - 1]} ${y}`;
};

// Conta os meses inclusive (como o LinkedIn): jul/2023 a out/2025 = 2 anos e 4 meses
const formatDuration = (start: string, end: string | null) => {
  const a = parseMonth(start);
  const now = new Date();
  const b = end ? parseMonth(end) : { y: now.getFullYear(), m: now.getMonth() + 1 };
  const total = (b.y - a.y) * 12 + (b.m - a.m) + 1;
  const years = Math.floor(total / 12);
  const months = total % 12;
  const parts = [];
  if (years) parts.push(`${years} ${years === 1 ? 'ano' : 'anos'}`);
  if (months) parts.push(`${months} ${months === 1 ? 'mês' : 'meses'}`);
  return parts.join(' e ');
};

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
  /** null = repositório ainda não publicado (botão aparece desativado) */
  repoUrl: string | null;
  videoUrl: string | null;
  siteUrl?: string;
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
    videoUrl: 'https://youtu.be/BUtwVJNfvFY',
    videoSoon: true,
    inProgress: true,
    accent: 'blue',
  },
  {
    id: 6,
    phase: 'Fase 4',
    label: 'Tech Challenge · Pós-Tech FIAP',
    title: 'Kubernetes na AWS',
    description:
      'A plataforma FIAP Cloud Games publicada na nuvem: microsserviços .NET rodando no Amazon Elastic Kubernetes Service, imagens no Amazon Elastic Container Registry e Elasticsearch, MongoDB e Redis no cluster.',
    thumbnail: '/projetos/tech-challenge-fase-4-aws.jpg',
    tags: ['Amazon EKS', 'ECR', 'Kubernetes', 'Elasticsearch', 'MongoDB', 'Redis'],
    repoUrl: null,
    videoUrl: null,
    inProgress: true,
    accent: 'amber',
  },
  {
    id: 5,
    phase: 'Hackathon',
    label: 'Tech Challenge · Pós-Tech FIAP',
    title: 'Conexão Solidária',
    description:
      'Plataforma de doações para a ONG Esperança Solidária: microsserviços .NET com autenticação JWT por perfil, doações processadas por eventos no RabbitMQ, deploy em Kubernetes, CI no GitHub Actions e monitoramento com Grafana e Zabbix.',
    thumbnail: '/projetos/hackathon.jpg',
    tags: ['.NET', 'RabbitMQ', 'Kubernetes', 'Grafana', 'Zabbix', 'GitHub Actions', 'JWT'],
    repoUrl: null,
    videoUrl: null,
    videoSoon: true,
    inProgress: true,
    accent: 'rose',
  },
];

const studyProjects: Project[] = [
  {
    id: 9,
    phase: 'Landing page',
    label: 'Cliente real · Angular',
    title: 'Simone Lamas Studio Hair',
    description:
      'Landing page do salão de beleza da minha mãe, com serviços, agendamento direto pelo WhatsApp, horários e mapa. Gerada como site estático, com SEO e dados estruturados.',
    thumbnail: '/projetos/studio-simonelamas.jpg',
    tags: ['Angular 21', 'Prerender (SSG)', 'Vercel', 'Cloudflare', 'SEO'],
    repoUrl: 'https://github.com/LMXSS/Studio-SimoneLamas',
    siteUrl: 'https://studio.simonelamas.com.br/',
    videoUrl: null,
    accent: 'pink',
  },
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
  {
    id: 8,
    phase: 'Web MVC',
    label: 'COTI Informática · 2022',
    title: 'Agenda de Contatos',
    description:
      'Aplicação ASP.NET MVC com contas de usuário, CRUD de contatos com foto, recuperação de senha por e-mail e relatórios em PDF e Excel. Um dos meus primeiros projetos completos em C#.',
    thumbnail: '/projetos/agendacontatos.jpg',
    tags: ['.NET 6', 'ASP.NET MVC', 'Dapper', 'SQL Server', 'iText', 'EPPlus'],
    repoUrl: 'https://github.com/LMXSS/AgendaContatos',
    videoUrl: null,
    accent: 'purple',
  },
];

const casteloProject: Project = {
  id: 10,
  phase: 'Landing page',
  label: 'Cliente real · Angular',
  title: 'Castelo das Frutas',
  description:
    'Landing page de um hortifrúti, com status de aberto agora calculado pelo horário de funcionamento, encarte de ofertas, vaga de emprego e informações de visita.',
  thumbnail: '/projetos/castelo-das-frutas.jpg',
  tags: ['Angular 22', 'Signals', 'SCSS', 'Vercel'],
  repoUrl: 'https://github.com/LMXSS/castelo-das-frutas',
  siteUrl: 'https://castelo-das-frutas.vercel.app/',
  videoUrl: null,
  accent: 'green',
};

// Todos os projetos juntos, em ordem de exibição no carrossel (landing pages de clientes no fim)
const [studioProject, ...otherStudyProjects] = studyProjects;
const projects: Project[] = [...techChallengeProjects, ...otherStudyProjects, studioProject!, casteloProject];

// Cores de destaque por projeto (estáticas p/ o Tailwind detectar)
const projectAccent: Record<string, { badge: string; hover: string }> = {
  violet: { badge: 'bg-violet-500/90 text-white', hover: 'hover:border-violet-500/50 hover:shadow-violet-500/10' },
  orange: { badge: 'bg-orange-500/90 text-white', hover: 'hover:border-orange-500/50 hover:shadow-orange-500/10' },
  emerald: { badge: 'bg-emerald-500/90 text-white', hover: 'hover:border-emerald-500/50 hover:shadow-emerald-500/10' },
  blue: { badge: 'bg-blue-500/90 text-white', hover: 'hover:border-blue-500/50 hover:shadow-blue-500/10' },
  amber: { badge: 'bg-amber-400 text-zinc-950', hover: 'hover:border-amber-400/50 hover:shadow-amber-400/10' },
  rose: { badge: 'bg-rose-500/90 text-white', hover: 'hover:border-rose-500/50 hover:shadow-rose-500/10' },
  lime: { badge: 'bg-lime-400 text-zinc-950', hover: 'hover:border-lime-400/50 hover:shadow-lime-400/10' },
  green: { badge: 'bg-green-400 text-zinc-950', hover: 'hover:border-green-400/50 hover:shadow-green-400/10' },
  pink: { badge: 'bg-pink-300 text-zinc-950', hover: 'hover:border-pink-300/50 hover:shadow-pink-300/10' },
  purple: { badge: 'bg-purple-400 text-zinc-950', hover: 'hover:border-purple-400/50 hover:shadow-purple-400/10' },
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
  {
    id: 4,
    name: 'Vagner Herdi',
    title: '',
    company: '',
    image: vagnerImage,
    linkedIn: 'https://www.linkedin.com/in/vagner-herdi/',
    message: 'Tive a oportunidade de trabalhar com ele e destaco a sua proatividade, capacidade de comunicação e competência técnica. É alguém que sabe construir pontes, alinhar expectativas e trabalhar muito bem em equipa. Procura sempre compreender e analisar bem os problemas antes de avançar para a implementação, o que faz toda a diferença na qualidade das soluções. Sem dúvida, um profissional que acrescenta valor à equipa e aos projetos em que trabalha.',
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
      <div class="absolute inset-0 z-0" aria-hidden="true">
        <DotField
          :dot-radius="1.5"
          :dot-spacing="14"
          :bulge-strength="67"
          :glow-radius="160"
          gradient-from="rgba(129, 140, 248, 0.40)"
          gradient-to="rgba(167, 139, 250, 0.22)"
          glow-color="#05060f"
        />
      </div>
      <div class="flex flex-col items-center text-center gap-5 relative z-20 px-6">
        <h1 class="w-[min(86vw,50rem)] h-[19vw] max-h-[170px] min-h-[72px]">
          <TechText
            :text="myName.replace(' ', '\u2009')"
            :font-weight="900"
            :font-size="160"
            :letter-spacing="-0.06"
            color="#f8fafc"
            last-word-color="#818cf8"
            accent-color="#a78bfa"
            :reach="200"
          />
        </h1>

        <div class="flex flex-col items-center gap-2">
          <p class="text-lg sm:text-2xl text-slate-100 font-semibold">{{ myTitle }}</p>
          <p class="text-sm sm:text-base text-slate-400 max-w-3xl leading-relaxed">
            <span v-for="line in myTagline" :key="line" class="sm:block">{{ line }} </span>
          </p>
        </div>

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
                <p class="text-2xl font-bold text-indigo-400">{{ techCountLabel }}</p>
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
      <div class="max-w-5xl mx-auto px-6">
        <div class="text-center mb-14">
          <p class="section-label mb-3">02 — Stack</p>
          <h2 class="text-4xl font-black text-foreground">Habilidades Técnicas</h2>
          <p class="text-muted-foreground mt-3 text-sm max-w-lg mx-auto leading-relaxed">
            Do back-end em .NET ao deploy na nuvem.
          </p>
        </div>

        <dl v-reveal class="border-t border-border">
          <div
            v-for="group in skillGroups"
            :key="group.title"
            class="grid gap-2 md:grid-cols-[14rem_1fr] md:gap-10 py-4 border-b border-border"
          >
            <dt class="font-mono text-xs uppercase tracking-[0.14em] text-muted-foreground md:pt-1">{{ group.title }}</dt>
            <dd class="flex flex-wrap gap-x-5 gap-y-1.5 text-[15px]">
              <span v-for="item in group.items" :key="item" class="skill-item">
                <span class="skill-item__ghost" aria-hidden="true">{{ item }}</span>
                <span class="skill-item__label">{{ item }}</span>
              </span>
            </dd>
          </div>
        </dl>

        <h3 class="mt-12 mb-1 text-lg font-bold text-foreground">Arquitetura e práticas</h3>
        <p class="mb-5 text-sm text-muted-foreground">Base da pós em Arquitetura de Sistemas .NET (FIAP).</p>
        <dl v-reveal class="border-t border-border">
          <div
            v-for="group in practiceGroups"
            :key="group.title"
            class="grid gap-2 md:grid-cols-[14rem_1fr] md:gap-10 py-4 border-b border-border"
          >
            <dt class="font-mono text-xs uppercase tracking-[0.14em] text-muted-foreground md:pt-1">{{ group.title }}</dt>
            <dd class="flex flex-wrap gap-x-5 gap-y-1.5 text-[15px]">
              <span v-for="item in group.items" :key="item" class="skill-item">
                <span class="skill-item__ghost" aria-hidden="true">{{ item }}</span>
                <span class="skill-item__label">{{ item }}</span>
              </span>
            </dd>
          </div>
        </dl>
      </div>
    </section>

    <!-- ─── FORMAÇÃO ───────────────────────────────── -->
    <section id="formacao" class="py-20 md:py-32 bg-background">
      <div class="max-w-4xl mx-auto px-6">
        <div class="text-center mb-14">
          <p class="section-label mb-3">03 — Educação</p>
          <h2 class="text-4xl font-black text-foreground">Formação</h2>
        </div>

        <ol class="border-t border-border">
          <li
            v-for="(edu, index) in educations"
            :key="edu.id"
            v-reveal="index"
            class="grid gap-1 md:grid-cols-[12.5rem_1fr] md:gap-10 py-6 border-b border-border"
          >
            <div class="md:pt-0.5">
              <p class="text-sm font-medium text-slate-200 tabular-nums md:whitespace-nowrap">{{ edu.duration }}</p>
              <p v-if="edu.upcoming" class="mt-1 font-mono text-xs text-emerald-400">Em andamento</p>
            </div>
            <div>
              <h3 class="text-base font-bold text-foreground leading-snug">{{ edu.course }}</h3>
              <p class="mt-1 text-sm text-muted-foreground">
                {{ edu.type }} · {{ edu.institution }} · {{ edu.location }}
              </p>
            </div>
          </li>
        </ol>
      </div>
    </section>

    <!-- ─── EXPERIÊNCIA ────────────────────────────── -->
    <section id="experiencia" class="py-20 md:py-32 bg-muted">
      <div class="max-w-4xl mx-auto px-6">
        <div class="text-center mb-14">
          <p class="section-label mb-3">04 — Carreira</p>
          <h2 class="text-4xl font-black text-foreground">Experiência Profissional</h2>
        </div>

        <ol class="border-t border-border">
          <li
            v-for="(exp, index) in experiences"
            :key="exp.id"
            v-reveal="index"
            class="grid gap-2 md:grid-cols-[12.5rem_1fr] md:gap-10 py-8 border-b border-border"
          >
            <div class="md:pt-0.5">
              <p class="text-sm font-medium text-slate-200 tabular-nums md:whitespace-nowrap">
                {{ formatMonth(exp.start) }} — <span :class="exp.end ? '' : 'text-emerald-400'">{{ exp.end ? formatMonth(exp.end) : 'Atual' }}</span>
              </p>
              <p class="mt-1 font-mono text-xs text-muted-foreground">{{ formatDuration(exp.start, exp.end) }}</p>
            </div>
            <div>
              <h3 class="text-lg font-bold text-foreground leading-snug">{{ exp.role }}</h3>
              <p class="mt-1 text-sm text-muted-foreground">
                <a
                  :href="exp.companyLink"
                  target="_blank"
                  rel="noopener noreferrer"
                  class="font-semibold text-indigo-400 hover:text-indigo-300 underline-offset-4 hover:underline"
                >{{ exp.company }}</a>
                · {{ exp.type }} · {{ exp.location }}
              </p>
              <p v-if="exp.summary" class="mt-4 text-[15px] text-slate-300 leading-relaxed max-w-2xl">{{ exp.summary }}</p>
              <ul v-if="exp.stack" class="mt-3 flex flex-wrap gap-x-3 gap-y-1 font-mono text-xs text-muted-foreground">
                <li v-for="tech in exp.stack" :key="tech" class="whitespace-nowrap">{{ tech }}</li>
              </ul>
            </div>
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
            Arquitetura, microsserviços, cloud, performance e ferramentas em .NET.
          </p>
        </div>

        <Carousel v-reveal :opts="{ align: 'start', loop: false }" class="w-full">
          <div class="flex items-center justify-between gap-4 mb-5">
            <p class="text-sm text-muted-foreground tabular-nums">{{ projects.length }} projetos</p>
            <div class="flex items-center gap-2">
              <CarouselPrevious class="static translate-y-0 translate-x-0 size-10 border-border bg-card hover:border-indigo-500/50 hover:bg-indigo-500/10 text-foreground" />
              <CarouselNext class="static translate-y-0 translate-x-0 size-10 border-border bg-card hover:border-indigo-500/50 hover:bg-indigo-500/10 text-foreground" />
            </div>
          </div>

          <CarouselContent class="-ml-5 py-2 items-stretch">
            <CarouselItem
              v-for="project in projects"
              :key="project.id"
              class="pl-5 basis-[88%] sm:basis-1/2 lg:basis-1/3"
            >
          <article
            :class="['group h-full flex flex-col rounded-2xl border border-border bg-card overflow-hidden transition-all duration-300 hover:-translate-y-1 hover:shadow-xl', projectAccent[project.accent]?.hover]"
          >
            <!-- Thumbnail -->
            <component
              :is="project.videoUrl || project.siteUrl || project.repoUrl ? 'a' : 'div'"
              :href="project.videoUrl ?? project.siteUrl ?? project.repoUrl ?? undefined"
              target="_blank"
              rel="noopener noreferrer"
              class="relative block aspect-video overflow-hidden bg-muted"
              :aria-label="project.videoUrl ? `Assistir vídeo: ${project.title}` : project.siteUrl ? `Abrir site: ${project.title}` : project.repoUrl ? `Ver repositório: ${project.title}` : undefined"
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
            </component>

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
                  Assistir
                </a>
                <span
                  v-else-if="project.videoSoon"
                  class="flex items-center gap-2 px-4 py-2 rounded-xl border border-border text-muted-foreground text-sm font-medium cursor-default"
                >
                  Vídeo em breve
                </span>
                <a
                  v-if="project.siteUrl"
                  :href="project.siteUrl"
                  target="_blank"
                  rel="noopener noreferrer"
                  class="flex items-center gap-2 px-4 py-2 rounded-xl bg-indigo-600 hover:bg-indigo-500 text-white font-semibold text-sm transition-all duration-200 hover:scale-[1.03] shadow-lg shadow-indigo-500/20"
                >
                  <Globe class="w-4 h-4" aria-hidden="true" />
                  Ver site
                </a>
                <a
                  v-if="project.repoUrl"
                  :href="project.repoUrl"
                  target="_blank"
                  rel="noopener noreferrer"
                  class="flex items-center gap-2 px-4 py-2 rounded-xl bg-zinc-800 hover:bg-zinc-700 text-white font-semibold text-sm transition-all duration-200 hover:scale-[1.03]"
                >
                  <i class="devicon-github-original text-base"></i>
                  Código
                </a>
                <span
                  v-else
                  class="flex items-center gap-2 px-4 py-2 rounded-xl bg-zinc-800/60 text-white/50 font-semibold text-sm cursor-not-allowed"
                  title="Repositório em breve"
                  aria-disabled="true"
                >
                  <i class="devicon-github-original text-base"></i>
                  Código
                </span>
              </div>
            </div>
          </article>
            </CarouselItem>
          </CarouselContent>
        </Carousel>
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
                        <p v-if="testimonial.title || testimonial.company" class="text-xs text-muted-foreground">
                          {{ testimonial.title }}
                          <span v-if="testimonial.company" class="text-indigo-400 font-medium"><template v-if="testimonial.title">· </template>{{ testimonial.company }}</span>
                        </p>
                      </div>
                      <i class="devicon-linkedin-plain text-blue-400 text-xl opacity-40 group-hover:opacity-100 transition-opacity flex-shrink-0"></i>
                    </div>
                  </CardContent>
                </Card>
              </a>
            </CarouselItem>
          </CarouselContent>
          <div class="flex justify-center gap-2 mt-6">
            <CarouselPrevious class="static translate-y-0 translate-x-0 size-10 border-border bg-card hover:border-indigo-500/50 hover:bg-indigo-500/10 text-foreground" />
            <CarouselNext class="static translate-y-0 translate-x-0 size-10 border-border bg-card hover:border-indigo-500/50 hover:bg-indigo-500/10 text-foreground" />
          </div>
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
