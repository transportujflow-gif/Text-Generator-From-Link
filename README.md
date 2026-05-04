import { useState, useEffect, useRef } from 'react';
import { 
  Search, 
  Brain, 
  History, 
  Settings, 
  Plus, 
  LayoutDashboard, 
  Terminal, 
  Cpu, 
  Globe, 
  FileText,
  TrendingUp,
  ExternalLink,
  ChevronRight,
  ArrowRight,
  Zap,
  Shield,
  Clock,
  MessageSquare,
  BarChart3
} from 'lucide-react';
import { motion } from 'framer-motion';
import { cn } from './utils/cn';
import { AreaChart, Area, XAxis, CartesianGrid, Tooltip, ResponsiveContainer } from 'recharts';

const mockChartData = [
  { name: 'Jan', value: 400 },
  { name: 'Feb', value: 300 },
  { name: 'Mar', value: 600 },
  { name: 'Apr', value: 800 },
  { name: 'May', value: 500 },
  { name: 'Jun', value: 900 },
  { name: 'Jul', value: 1100 },
];

// --- Types ---
interface ResearchStep {
  id: string;
  status: 'pending' | 'loading' | 'completed' | 'error';
  label: string;
  details?: string;
  timestamp: string;
}

interface ResearchResult {
  title: string;
  summary: string;
  sources: { title: string; url: string; snippet: string }[];
  insights: string[];
  sentiment: 'positive' | 'neutral' | 'negative';
}

// --- Components ---

const Sidebar = ({ activeTab, setActiveTab }: { activeTab: string; setActiveTab: (t: string) => void }) => {
  const tabs = [
    { id: 'dashboard', icon: LayoutDashboard, label: 'Dashboard' },
    { id: 'history', icon: History, label: 'Research History' },
    { id: 'agents', icon: Cpu, label: 'Agent Fleet' },
    { id: 'settings', icon: Settings, label: 'Settings' },
  ];

  return (
    <div className="w-64 h-screen bg-slate-900 border-r border-slate-800 flex flex-col p-4 text-slate-300">
      <div className="flex items-center gap-2 px-2 py-6 mb-4">
        <div className="bg-indigo-600 p-2 rounded-lg">
          <Brain className="w-6 h-6 text-white" />
        </div>
        <span className="text-xl font-bold text-white tracking-tight">Nexus AI</span>
      </div>

      <nav className="flex-1 space-y-1">
        {tabs.map((tab) => (
          <button
            key={tab.id}
            onClick={() => setActiveTab(tab.id)}
            className={cn(
              "w-full flex items-center gap-3 px-3 py-2.5 rounded-lg transition-colors text-sm font-medium",
              activeTab === tab.id 
                ? "bg-slate-800 text-white shadow-sm" 
                : "hover:bg-slate-800/50 hover:text-slate-100"
            )}
          >
            <tab.icon className="w-5 h-5" />
            {tab.label}
          </button>
        ))}
      </nav>

      <div className="mt-auto pt-4 border-t border-slate-800">
        <div className="bg-gradient-to-br from-indigo-900/40 to-slate-800 p-4 rounded-xl border border-indigo-500/20">
          <p className="text-xs font-semibold text-indigo-400 uppercase tracking-wider mb-1">Pro Plan</p>
          <p className="text-sm text-slate-300 mb-3">500/1000 research credits used</p>
          <div className="h-1.5 bg-slate-700 rounded-full overflow-hidden">
            <div className="h-full bg-indigo-500 w-1/2 rounded-full" />
          </div>
        </div>
      </div>
    </div>
  );
};

export default function App() {
  const [activeTab, setActiveTab] = useState('dashboard');
  const [query, setQuery] = useState('');
  const [isResearching, setIsResearching] = useState(false);
  const [steps, setSteps] = useState<ResearchStep[]>([]);
  const [result, setResult] = useState<ResearchResult | null>(null);
  const scrollRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (scrollRef.current) {
      scrollRef.current.scrollTop = scrollRef.current.scrollHeight;
    }
  }, [steps]);

  const handleResearch = async () => {
    if (!query.trim()) return;
    
    setIsResearching(true);
    setResult(null);
    setSteps([]);

    const mockSteps: { label: string; details: string }[] = [
      { label: 'Initializing Research Agent', details: 'Spinning up instances in us-east-1...' },
      { label: 'Analyzing Query Context', details: 'Extracting key entities and search parameters...' },
      { label: 'Expanding Search Queries', details: 'Generating 12 semantic search variations...' },
      { label: 'Scanning Web Indices', details: 'Searching Google, Bing, and ArXiv...' },
      { label: 'Scraping Content', details: 'Extracting clean text from 8 relevant domains...' },
      { label: 'Analyzing Data Sentiment', details: 'Running sentiment analysis across news outlets...' },
      { label: 'Synthesizing Findings', details: 'Applying GPT-4o to consolidate information...' },
      { label: 'Verifying Facts', details: 'Cross-referencing claims with trusted sources...' },
    ];

    for (let i = 0; i < mockSteps.length; i++) {
      const step = mockSteps[i];
      setSteps(prev => [
        ...prev.map(s => s.status === 'loading' ? { ...s, status: 'completed' as const } : s),
        { 
          id: i.toString(), 
          status: 'loading', 
          label: step.label, 
          details: step.details, 
          timestamp: new Date().toLocaleTimeString() 
        }
      ]);
      await new Promise(r => setTimeout(r, 1200 + Math.random() * 1000));
    }

    setSteps(prev => prev.map(s => ({ ...s, status: 'completed' as const })));
    
    setResult({
      title: `Research Report: ${query}`,
      summary: "Based on current trends and technical documentation, this topic is witnessing significant growth and investment. Recent breakthroughs in core architectures have led to a 40% increase in efficiency across major implementations. Industry leaders are pivoting towards sustainable deployment models while maintaining high performance metrics.",
      sources: [
        { title: "Future of Technology 2024", url: "https://tech-insights.com/report-1", snippet: "Recent data suggests a paradigm shift in how we approach this specific technology..." },
        { title: "Journal of Innovation", url: "https://science-journal.org/paper-44", snippet: "The mathematical foundations for these advancements were first proposed in early 2023..." },
        { title: "Industry Analysis: Q3 Review", url: "https://market-watch.com/analysis", snippet: "Competitors are rapidly adopting these new standards to stay relevant in the evolving market..." }
      ],
      insights: [
        "Rapid adoption in enterprise sectors within the last 6 months.",
        "Development costs have dropped significantly due to open-source tooling.",
        "Security remains the primary concern for large-scale deployments.",
        "Anticipated regulatory changes might impact future growth trajectories."
      ],
      sentiment: 'positive'
    });
    
    setIsResearching(false);
  };

  return (
    <div className="flex min-h-screen bg-slate-950 text-slate-100 font-sans selection:bg-indigo-500/30">
      <Sidebar activeTab={activeTab} setActiveTab={setActiveTab} />

      <main className="flex-1 flex flex-col h-screen overflow-hidden">
        {/* Header */}
        <header className="h-16 border-b border-slate-800 flex items-center justify-between px-8 bg-slate-950/50 backdrop-blur-md z-10">
          <div className="flex items-center gap-2">
            <span className="text-sm text-slate-400 font-medium">Dashboard</span>
            <ChevronRight className="w-4 h-4 text-slate-600" />
            <span className="text-sm font-semibold">AI Researcher</span>
          </div>
          <div className="flex items-center gap-4">
            <div className="flex items-center gap-2 px-3 py-1.5 bg-slate-900 rounded-full border border-slate-800">
              <div className="w-2 h-2 rounded-full bg-green-500 animate-pulse" />
              <span className="text-xs text-slate-300 font-medium">Agent Fleet Online</span>
            </div>
            <button className="p-2 hover:bg-slate-800 rounded-lg transition-colors">
              <MessageSquare className="w-5 h-5 text-slate-400" />
            </button>
            <div className="w-8 h-8 rounded-full bg-gradient-to-tr from-indigo-500 to-purple-500 border border-slate-700" />
          </div>
        </header>

        {/* Content */}
        <div className="flex-1 overflow-y-auto p-8 bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))] from-slate-900 via-slate-950 to-slate-950 relative">
          <div className="absolute top-0 left-1/4 w-96 h-96 bg-indigo-600/10 blur-[100px] rounded-full -translate-y-1/2 pointer-events-none" />
          <div className="absolute bottom-0 right-1/4 w-96 h-96 bg-purple-600/10 blur-[100px] rounded-full translate-y-1/2 pointer-events-none" />
          
          <div className="max-w-4xl mx-auto relative z-10">
            {activeTab === 'dashboard' && (
              <div className="space-y-8">
                {/* Search Hero */}
                {!result && !isResearching && (
                  <motion.div 
                    initial={{ opacity: 0, y: 20 }}
                    animate={{ opacity: 1, y: 0 }}
                    className="text-center py-20 space-y-6"
                  >
                    <div className="inline-flex items-center gap-2 px-4 py-2 bg-indigo-500/10 text-indigo-400 rounded-full border border-indigo-500/20 mb-4">
                      <Zap className="w-4 h-4" />
                      <span className="text-xs font-bold uppercase tracking-widest">Next-Gen Intelligence</span>
                    </div>
                    <h1 className="text-5xl font-extrabold tracking-tight text-white mb-4">
                      What do you want to <br/>
                      <span className="text-transparent bg-clip-text bg-gradient-to-r from-indigo-400 via-purple-400 to-indigo-400 animate-gradient">research today?</span>
                    </h1>
                    <p className="text-slate-400 text-lg max-w-xl mx-auto leading-relaxed">
                      Enter a topic, industry, or specific URL. Our AI agents will scour the web, analyze data, and provide deep insights in seconds.
                    </p>

                    <div className="max-w-2xl mx-auto mt-12 relative group">
                      <div className="absolute -inset-1 bg-gradient-to-r from-indigo-500 to-purple-600 rounded-2xl blur opacity-25 group-hover:opacity-50 transition duration-1000 group-hover:duration-200"></div>
                      <div className="relative flex items-center gap-2 bg-slate-900 p-2 rounded-2xl border border-slate-700 shadow-2xl">
                        <Search className="w-6 h-6 text-slate-500 ml-4" />
                        <input 
                          type="text" 
                          placeholder="Search 'Impact of AI on SaaS pricing' or 'Tesla Q4 analysis'..."
                          className="flex-1 bg-transparent border-none focus:ring-0 text-white placeholder-slate-500 py-4 text-lg"
                          value={query}
                          onChange={(e) => setQuery(e.target.value)}
                          onKeyDown={(e) => e.key === 'Enter' && handleResearch()}
                        />
                        <button 
                          onClick={handleResearch}
                          className="bg-indigo-600 hover:bg-indigo-500 text-white px-6 py-4 rounded-xl font-bold transition-all flex items-center gap-2 active:scale-95"
                        >
                          Launch Agent
                          <ArrowRight className="w-5 h-5" />
                        </button>
                      </div>
                    </div>

                    <div className="flex flex-wrap justify-center gap-3 mt-12 text-sm text-slate-500">
                      <span>Try searching:</span>
                      {['Nvidia Roadmap', 'Web3 Trends', 'SaaS Metrics', 'BioTech News'].map((suggestion) => (
                        <button 
                          key={suggestion}
                          onClick={() => setQuery(suggestion)}
                          className="px-3 py-1 bg-slate-800/50 rounded-lg hover:bg-slate-800 transition-colors border border-slate-700/50"
                        >
                          {suggestion}
                        </button>
                      ))}
                    </div>
                  </motion.div>
                )}

                {/* Researching State */}
                {isResearching && (
                  <div className="space-y-8 max-w-2xl mx-auto">
                    <div className="bg-slate-900/50 border border-slate-800 rounded-2xl overflow-hidden backdrop-blur-sm">
                      <div className="p-6 border-b border-slate-800 flex items-center justify-between">
                        <div className="flex items-center gap-3">
                          <div className="bg-indigo-600 p-2 rounded-lg animate-pulse">
                            <Terminal className="w-5 h-5 text-white" />
                          </div>
                          <div>
                            <h3 className="font-bold text-white">Agent Activity Log</h3>
                            <p className="text-xs text-slate-400">Processing: {query}</p>
                          </div>
                        </div>
                        <div className="text-xs text-slate-500 font-mono">
                          STATUS: WORKING
                        </div>
                      </div>
                      <div className="p-6 h-80 overflow-y-auto font-mono text-sm space-y-3" ref={scrollRef}>
                        {steps.map((step) => (
                          <motion.div 
                            initial={{ opacity: 0, x: -10 }}
                            animate={{ opacity: 1, x: 0 }}
                            key={step.id} 
                            className="flex items-start gap-3"
                          >
                            <span className="text-slate-600">[{step.timestamp}]</span>
                            <div className="flex-1">
                              <div className="flex items-center gap-2">
                                {step.status === 'loading' ? (
                                  <div className="w-3 h-3 border-2 border-indigo-500 border-t-transparent rounded-full animate-spin" />
                                ) : (
                                  <div className="w-3 h-3 bg-green-500 rounded-full" />
                                )}
                                <span className={cn(
                                  "font-bold",
                                  step.status === 'loading' ? "text-indigo-400" : "text-green-400"
                                )}>
                                  {step.label}
                                </span>
                              </div>
                              <p className="text-slate-500 mt-1 pl-5">{step.details}</p>
                            </div>
                          </motion.div>
                        ))}
                      </div>
                    </div>
                  </div>
                )}

                {/* Results State */}
                {result && !isResearching && (
                  <motion.div 
                    initial={{ opacity: 0 }}
                    animate={{ opacity: 1 }}
                    className="space-y-8"
                  >
                    <div className="flex items-center justify-between">
                      <h2 className="text-3xl font-bold text-white">{result.title}</h2>
                      <div className="flex gap-2">
                        <button 
                          onClick={() => { setResult(null); setQuery(''); }}
                          className="px-4 py-2 bg-slate-800 hover:bg-slate-700 rounded-lg text-sm font-medium transition-colors"
                        >
                          New Research
                        </button>
                        <button className="px-4 py-2 bg-indigo-600 hover:bg-indigo-500 rounded-lg text-sm font-bold transition-colors flex items-center gap-2">
                          <FileText className="w-4 h-4" />
                          Export PDF
                        </button>
                      </div>
                    </div>

                    <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
                      <div className="md:col-span-2 space-y-6">
                        {/* Executive Summary */}
                        <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl">
                          <div className="flex items-center gap-2 mb-4 text-indigo-400">
                            <FileText className="w-5 h-5" />
                            <h3 className="font-bold uppercase tracking-wider text-xs">Executive Summary</h3>
                          </div>
                          <p className="text-slate-300 leading-relaxed">
                            {result.summary}
                          </p>
                        </div>

                        {/* Key Insights */}
                        <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
                          {result.insights.map((insight, idx) => (
                            <div key={idx} className="bg-slate-900/50 border border-slate-800 p-4 rounded-xl flex items-start gap-3">
                              <div className="bg-indigo-500/20 p-1.5 rounded-lg text-indigo-400 mt-0.5">
                                <TrendingUp className="w-4 h-4" />
                              </div>
                              <p className="text-sm text-slate-300">{insight}</p>
                            </div>
                          ))}
                        </div>

                        {/* Sources */}
                        <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6">
                          <div className="flex items-center gap-2 mb-6 text-indigo-400">
                            <Globe className="w-5 h-5" />
                            <h3 className="font-bold uppercase tracking-wider text-xs">Verified Sources</h3>
                          </div>
                          <div className="space-y-4">
                            {result.sources.map((source, idx) => (
                              <div key={idx} className="group border-b border-slate-800 pb-4 last:border-0 last:pb-0">
                                <a href={source.url} target="_blank" className="flex items-center justify-between group-hover:text-indigo-400 transition-colors">
                                  <h4 className="font-bold text-slate-200">{source.title}</h4>
                                  <ExternalLink className="w-4 h-4 text-slate-600 group-hover:text-indigo-400" />
                                </a>
                                <p className="text-xs text-indigo-500 mt-1 mb-2 font-mono">{source.url}</p>
                                <p className="text-sm text-slate-400 line-clamp-2">{source.snippet}</p>
                              </div>
                            ))}
                          </div>
                        </div>
                      </div>

                      <div className="space-y-6">
                        {/* Sentiment Analysis */}
                        <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6">
                          <h3 className="text-sm font-bold text-slate-400 mb-6 uppercase tracking-wider">Analysis Overview</h3>
                          <div className="space-y-6">
                            <div>
                              <div className="flex justify-between text-xs mb-2">
                                <span className="text-slate-500">Market Sentiment</span>
                                <span className="text-green-400 font-bold uppercase">Optimistic</span>
                              </div>
                              <div className="h-2 bg-slate-800 rounded-full overflow-hidden">
                                <div className="h-full bg-green-500 w-[78%] shadow-[0_0_10px_rgba(34,197,94,0.5)]" />
                              </div>
                            </div>
                            <div>
                              <div className="flex justify-between text-xs mb-2">
                                <span className="text-slate-500">Confidence Score</span>
                                <span className="text-indigo-400 font-bold">92%</span>
                              </div>
                              <div className="h-2 bg-slate-800 rounded-full overflow-hidden">
                                <div className="h-full bg-indigo-500 w-[92%] shadow-[0_0_10px_rgba(99,102,241,0.5)]" />
                              </div>
                            </div>
                            <div>
                              <div className="flex justify-between text-xs mb-2">
                                <span className="text-slate-500">Data Reliability</span>
                                <span className="text-purple-400 font-bold">High</span>
                              </div>
                              <div className="h-2 bg-slate-800 rounded-full overflow-hidden">
                                <div className="h-full bg-purple-500 w-[85%] shadow-[0_0_10px_rgba(168,85,247,0.5)]" />
                              </div>
                            </div>
                          </div>
                        </div>

                        {/* Trend Analysis Chart */}
                        <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6">
                          <div className="flex items-center justify-between mb-6">
                            <div className="flex items-center gap-2 text-indigo-400">
                              <BarChart3 className="w-5 h-5" />
                              <h3 className="font-bold uppercase tracking-wider text-xs">Interest Over Time</h3>
                            </div>
                          </div>
                          <div className="h-48 w-full">
                            <ResponsiveContainer width="100%" height="100%">
                              <AreaChart data={mockChartData}>
                                <defs>
                                  <linearGradient id="colorValue" x1="0" y1="0" x2="0" y2="1">
                                    <stop offset="5%" stopColor="#6366f1" stopOpacity={0.3}/>
                                    <stop offset="95%" stopColor="#6366f1" stopOpacity={0}/>
                                  </linearGradient>
                                </defs>
                                <CartesianGrid strokeDasharray="3 3" stroke="#1e293b" vertical={false} />
                                <XAxis 
                                  dataKey="name" 
                                  stroke="#475569" 
                                  fontSize={10} 
                                  tickLine={false} 
                                  axisLine={false} 
                                />
                                <Tooltip 
                                  contentStyle={{ backgroundColor: '#0f172a', border: '1px solid #1e293b', borderRadius: '8px', fontSize: '12px' }}
                                  itemStyle={{ color: '#fff' }}
                                />
                                <Area 
                                  type="monotone" 
                                  dataKey="value" 
                                  stroke="#6366f1" 
                                  fillOpacity={1} 
                                  fill="url(#colorValue)" 
                                  strokeWidth={2}
                                />
                              </AreaChart>
                            </ResponsiveContainer>
                          </div>
                        </div>

                        {/* Quick Stats */}
                        <div className="bg-indigo-600 rounded-2xl p-6 shadow-xl shadow-indigo-500/20 text-white">
                          <div className="flex items-center gap-3 mb-6">
                            <Shield className="w-8 h-8 opacity-50" />
                            <h3 className="font-bold">Research Stats</h3>
                          </div>
                          <div className="space-y-4">
                            <div className="flex justify-between items-center">
                              <div className="flex items-center gap-2 text-indigo-100 text-xs font-medium">
                                <Clock className="w-4 h-4" /> Process Time
                              </div>
                              <span className="font-bold">8.4s</span>
                            </div>
                            <div className="flex justify-between items-center">
                              <div className="flex items-center gap-2 text-indigo-100 text-xs font-medium">
                                <Globe className="w-4 h-4" /> Sites Scanned
                              </div>
                              <span className="font-bold">142</span>
                            </div>
                            <div className="flex justify-between items-center">
                              <div className="flex items-center gap-2 text-indigo-100 text-xs font-medium">
                                <Terminal className="w-4 h-4" /> Agent Tokens
                              </div>
                              <span className="font-bold">2.4k</span>
                            </div>
                          </div>
                        </div>

                        {/* Next Steps */}
                        <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6">
                          <h3 className="text-sm font-bold text-slate-400 mb-4 uppercase tracking-wider">Suggested Queries</h3>
                          <div className="space-y-2">
                            {['Regulatory impact', 'Top 5 competitors', 'Future predictions'].map((q) => (
                              <button key={q} className="w-full text-left p-3 rounded-lg hover:bg-slate-800 text-sm text-slate-300 flex items-center justify-between group">
                                {q}
                                <ChevronRight className="w-4 h-4 text-slate-600 group-hover:text-indigo-400" />
                              </button>
                            ))}
                          </div>
                        </div>
                      </div>
                    </div>
                  </motion.div>
                )}
              </div>
            )}

            {activeTab === 'history' && (
              <div className="space-y-6">
                <h2 className="text-2xl font-bold text-white mb-8">Research History</h2>
                {[
                  { title: "Apple Vision Pro Market Analysis", date: "2 hours ago", status: "Success" },
                  { title: "Rust vs Go for Microservices", date: "Yesterday", status: "Success" },
                  { title: "Quantum Computing Hardware Trends", date: "3 days ago", status: "Success" },
                  { title: "LLM Hallucination Reduction Techniques", date: "Last week", status: "Success" }
                ].map((item, i) => (
                  <div key={i} className="bg-slate-900 border border-slate-800 p-5 rounded-xl flex items-center justify-between hover:border-slate-700 transition-colors group cursor-pointer">
                    <div className="flex items-center gap-4">
                      <div className="bg-slate-800 p-3 rounded-lg text-slate-400 group-hover:text-indigo-400 transition-colors">
                        <History className="w-6 h-6" />
                      </div>
                      <div>
                        <h4 className="font-bold text-slate-200">{item.title}</h4>
                        <p className="text-sm text-slate-500">{item.date}</p>
                      </div>
                    </div>
                    <div className="flex items-center gap-4">
                      <span className="text-xs font-bold text-green-500 bg-green-500/10 px-2 py-1 rounded border border-green-500/20">{item.status}</span>
                      <ChevronRight className="w-5 h-5 text-slate-600" />
                    </div>
                  </div>
                ))}
              </div>
            )}
            
            {activeTab === 'agents' && (
              <div className="space-y-6">
                <div className="flex justify-between items-end mb-8">
                  <div>
                    <h2 className="text-2xl font-bold text-white">Agent Fleet</h2>
                    <p className="text-slate-400">Configure and monitor your autonomous researchers.</p>
                  </div>
                  <button className="bg-indigo-600 hover:bg-indigo-500 text-white px-4 py-2 rounded-lg font-bold transition-all flex items-center gap-2">
                    <Plus className="w-4 h-4" />
                    Deploy New Agent
                  </button>
                </div>
                
                <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                  {[
                    { name: "Web Crawler Alpha", type: "Search & Scrape", status: "Active", uptime: "99.9%" },
                    { name: "Insight Engine V2", type: "Data Synthesis", status: "Active", uptime: "98.5%" },
                    { name: "Fact Checker", type: "Verification", status: "Active", uptime: "100%" },
                    { name: "Sentiment Bot", type: "NLP Analysis", status: "Idle", uptime: "94.2%" }
                  ].map((agent, i) => (
                    <div key={i} className="bg-slate-900 border border-slate-800 p-6 rounded-2xl space-y-4">
                      <div className="flex justify-between items-start">
                        <div className="bg-indigo-500/10 p-3 rounded-xl text-indigo-400">
                          <Cpu className="w-6 h-6" />
                        </div>
                        <div className={cn(
                          "px-2 py-1 rounded text-[10px] font-bold uppercase tracking-wider border",
                          agent.status === 'Active' ? "text-green-500 border-green-500/20 bg-green-500/10" : "text-slate-500 border-slate-800 bg-slate-800"
                        )}>
                          {agent.status}
                        </div>
                      </div>
                      <div>
                        <h4 className="font-bold text-white text-lg">{agent.name}</h4>
                        <p className="text-sm text-slate-400">{agent.type}</p>
                      </div>
                      <div className="pt-4 border-t border-slate-800 flex justify-between items-center text-xs">
                        <span className="text-slate-500">Uptime: <span className="text-slate-200">{agent.uptime}</span></span>
                        <button className="text-indigo-400 font-bold hover:underline">Configure</button>
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            )}
          </div>
        </div>
      </main>
    </div>
  );
}
