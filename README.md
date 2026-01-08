import React, { useState, useEffect, useRef } from 'react';
import { 
  CheckCircle, 
  User, 
  Building2, 
  Mail, 
  Smartphone, 
  Zap, 
  Sparkles, 
  Box, 
  Type, 
  TrendingUp, 
  Clock, 
  Cpu, 
  Target, 
  PlayCircle, 
  X 
} from 'lucide-react';

/* =================================================================================
   COMPONENTES AUXILIARES (MODAIS, BACKGROUND, EFEITOS)
   ================================================================================= */

/**
 * COMPONENTE DE VIDEO MODAL
 * Exibe o vídeo do Streamable em um popup vertical
 */
const VideoModal = ({ isOpen, onClose }) => {
  if (!isOpen) return null;

  return (
    <div className="fixed inset-0 z-[60] flex items-center justify-center p-4 bg-black/90 backdrop-blur-sm animate-fade-in" onClick={onClose}>
      <div 
        className="relative w-full max-w-[400px] aspect-[9/16] bg-black rounded-2xl overflow-hidden border border-white/10 shadow-[0_0_50px_rgba(139,92,246,0.3)]"
        onClick={(e) => e.stopPropagation()}
      >
        <button 
          onClick={onClose} 
          className="absolute top-4 right-4 z-20 p-2 bg-black/50 text-white rounded-full hover:bg-white/20 transition-colors backdrop-blur-md border border-white/10"
        >
          <X size={20} />
        </button>
        <iframe 
          width="100%" 
          height="100%" 
          src="https://streamable.com/e/a6l2qp?autoplay=1" 
          title="Video IA Exemplo" 
          frameBorder="0" 
          allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
          allowFullScreen
          className="w-full h-full"
        ></iframe>
      </div>
    </div>
  );
};

/**
 * COMPONENTE DE PRE-LOADER (INTRO)
 * Animação inicial com frase de impacto
 */
const Preloader = ({ onComplete }) => {
  const canvasRef = useRef(null);
  const [textOpacity, setTextOpacity] = useState(0);
  const [isExiting, setIsExiting] = useState(false);

  useEffect(() => {
    // Sequência de animação
    setTimeout(() => setTextOpacity(1), 500); 
    setTimeout(() => {
        setIsExiting(true); 
        setTimeout(onComplete, 800); 
    }, 4500); 

    const canvas = canvasRef.current;
    if (!canvas) return;
    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    let w = (canvas.width = window.innerWidth);
    let h = (canvas.height = window.innerHeight);
    
    let particles = [];
    const particleCount = window.innerWidth < 768 ? 80 : 150;
    const cx = w / 2;
    const cy = h / 2;

    const handleResize = () => {
      w = canvas.width = window.innerWidth;
      h = canvas.height = window.innerHeight;
    };
    window.addEventListener('resize', handleResize);

    for(let i=0; i<particleCount; i++) {
        particles.push({
            x: Math.random() * w,
            y: Math.random() * h,
            vx: (Math.random() - 0.5) * 2,
            vy: (Math.random() - 0.5) * 2,
            size: Math.random() * 2
        });
    }

    let animationFrame;

    const animate = () => {
        if (!ctx) return;
        ctx.fillStyle = 'rgba(0, 0, 0, 0.2)';
        ctx.fillRect(0, 0, w, h);

        particles.forEach(p => {
            p.x += p.vx;
            p.y += p.vy;

            const dx = cx - p.x;
            const dy = cy - p.y;
            const dist = Math.sqrt(dx*dx + dy*dy);
            
            if (dist > 100) { 
                p.vx += dx * 0.0005;
                p.vy += dy * 0.0005;
            } else {
                p.vx += (Math.random() - 0.5) * 0.5;
                p.vy += (Math.random() - 0.5) * 0.5;
            }

            p.vx *= 0.96;
            p.vy *= 0.96;

            ctx.beginPath();
            ctx.fillStyle = dist < 200 ? '#fff' : '#444'; 
            ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
            ctx.fill();

            particles.forEach(p2 => {
                const d2 = Math.sqrt((p.x - p2.x)**2 + (p.y - p2.y)**2);
                if (d2 < 80) {
                    ctx.beginPath();
                    const isCenter = dist < 250;
                    ctx.strokeStyle = isCenter 
                        ? `rgba(6, 182, 212, ${0.3 * (1 - d2/80)})` 
                        : `rgba(50, 50, 50, ${0.1 * (1 - d2/80)})`;
                    ctx.lineWidth = 0.5;
                    ctx.moveTo(p.x, p.y);
                    ctx.lineTo(p2.x, p2.y);
                    ctx.stroke();
                }
            });
        });

        if (!isExiting) {
            animationFrame = requestAnimationFrame(animate);
        }
    };

    animate();

    return () => {
        window.removeEventListener('resize', handleResize);
        cancelAnimationFrame(animationFrame);
    };
  }, [isExiting, onComplete]);

  return (
    <div className={`fixed inset-0 z-50 bg-black flex items-center justify-center transition-opacity duration-1000 ${isExiting ? 'opacity-0 pointer-events-none' : 'opacity-100'}`}>
      <canvas ref={canvasRef} className="absolute inset-0 w-full h-full" />
      
      <div 
        className="relative z-10 max-w-2xl px-6 text-center transition-all duration-1000 transform"
        style={{ opacity: textOpacity, transform: textOpacity ? 'scale(1)' : 'scale(0.95)' }}
      >
        <h2 className="text-3xl md:text-5xl font-black text-white leading-tight tracking-tighter drop-shadow-[0_0_15px_rgba(255,255,255,0.5)]">
          Cansou de <span className="text-transparent bg-clip-text bg-gradient-to-r from-gray-500 via-gray-300 to-gray-500">criativos ruins</span> <br/>
          que nunca convertem?
        </h2>
        <div className="w-24 h-1 bg-gray-800 mx-auto mt-6 rounded-full overflow-hidden">
            <div className="h-full bg-cyan-500 animate-loading-bar"></div>
        </div>
      </div>
      
      <style>{`
        @keyframes loading-bar {
            0% { width: 0%; transform: translateX(-100%); }
            50% { width: 50%; }
            100% { width: 100%; transform: translateX(100%); }
        }
        .animate-loading-bar {
            animation: loading-bar 2s infinite linear;
        }
      `}</style>
    </div>
  );
};

/**
 * COMPONENTE DE FUNDO ANIMADO (CANVAS) - NEURAL NETWORK
 */
const TechBackground = () => {
  const canvasRef = useRef(null);

  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;
    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    let w = (canvas.width = window.innerWidth);
    let h = (canvas.height = window.innerHeight);
    let particles = [];
    
    const particleCount = window.innerWidth < 768 ? 60 : 100;
    const connectionDistance = 150;
    const mouseDistance = 200;

    let mouse = { x: -1000, y: -1000 };

    const handleResize = () => {
      w = canvas.width = window.innerWidth;
      h = canvas.height = window.innerHeight;
    };
    
    const handleMouseMove = (e) => {
      mouse.x = e.clientX;
      mouse.y = e.clientY;
    };

    window.addEventListener('resize', handleResize);
    window.addEventListener('mousemove', handleMouseMove);

    class Particle {
      constructor() {
        this.x = Math.random() * w;
        this.y = Math.random() * h;
        this.vx = (Math.random() - 0.5) * 0.5;
        this.vy = (Math.random() - 0.5) * 0.5;
        this.size = Math.random() * 2 + 1;
      }

      update() {
        this.x += this.vx;
        this.y += this.vy;
        if (this.x < 0 || this.x > w) this.vx *= -1;
        if (this.y < 0 || this.y > h) this.vy *= -1;

        const dx = mouse.x - this.x;
        const dy = mouse.y - this.y;
        const distance = Math.sqrt(dx*dx + dy*dy);
        
        if (distance < mouseDistance) {
            const forceDirectionX = dx / distance;
            const forceDirectionY = dy / distance;
            const force = (mouseDistance - distance) / mouseDistance;
            this.vx -= forceDirectionX * force * 0.05;
            this.vy -= forceDirectionY * force * 0.05;
        }
      }

      draw() {
        if (!ctx) return;
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
        ctx.fillStyle = 'rgba(255, 255, 255, 0.5)';
        ctx.fill();
      }
    }

    for (let i = 0; i < particleCount; i++) {
      particles.push(new Particle());
    }

    const animate = () => {
      if (!ctx) return;
      ctx.clearRect(0, 0, w, h);
      
      const gradient = ctx.createLinearGradient(0, 0, 0, h);
      gradient.addColorStop(0, '#020202');
      gradient.addColorStop(1, '#0a0a10');
      ctx.fillStyle = gradient;
      ctx.fillRect(0, 0, w, h);

      particles.forEach(p => {
        p.update();
        p.draw();
      });

      connectParticles();
      requestAnimationFrame(animate);
    };

    const connectParticles = () => {
        if (!ctx) return;
        for (let a = 0; a < particles.length; a++) {
            for (let b = a; b < particles.length; b++) {
                const dx = particles[a].x - particles[b].x;
                const dy = particles[a].y - particles[b].y;
                const distance = Math.sqrt(dx * dx + dy * dy);

                if (distance < connectionDistance) {
                    const opacity = 1 - (distance / connectionDistance);
                    ctx.beginPath();
                    ctx.strokeStyle = `rgba(100, 100, 255, ${opacity * 0.15})`;
                    ctx.lineWidth = 1;
                    ctx.moveTo(particles[a].x, particles[a].y);
                    ctx.lineTo(particles[b].x, particles[b].y);
                    ctx.stroke();
                }
            }
        }
    }

    animate();

    return () => {
        window.removeEventListener('resize', handleResize);
        window.removeEventListener('mousemove', handleMouseMove);
    };
  }, []);

  return (
    <div className="fixed inset-0 z-0 overflow-hidden pointer-events-none">
      <canvas ref={canvasRef} className="absolute inset-0 w-full h-full" />
      <div className="absolute inset-0 bg-[radial-gradient(circle_at_center,transparent_0%,#000000_100%)] opacity-80"></div>
    </div>
  );
};

/**
 * COMPONENTE DO BOTÃO NEURAL (CANVAS INTERNO)
 */
const NeuralButton = ({ onClick, disabled, status }) => {
  const canvasRef = useRef(null);
  const containerRef = useRef(null);

  useEffect(() => {
    const canvas = canvasRef.current;
    const container = containerRef.current;
    if (!canvas || !container) return;
    
    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    const rect = container.getBoundingClientRect();
    canvas.width = rect.width;
    canvas.height = rect.height;

    let particles = [];
    let mouse = { x: -100, y: -100 };
    
    for(let i=0; i<30; i++) {
        particles.push({
            x: Math.random() * canvas.width,
            y: Math.random() * canvas.height,
            vx: (Math.random() - 0.5),
            vy: (Math.random() - 0.5)
        });
    }

    const handleMouseMove = (e) => {
        if (!containerRef.current) return;
        const rect = containerRef.current.getBoundingClientRect();
        mouse.x = e.clientX - rect.left;
        mouse.y = e.clientY - rect.top;
    };

    container.addEventListener('mousemove', handleMouseMove);
    container.addEventListener('mouseleave', () => { mouse.x = -100; mouse.y = -100; });

    let animationFrameId;

    const animate = () => {
        if (!ctx) return;
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        const isFilling = status === 'filling';
        
        if (isFilling) {
             ctx.fillStyle = `rgba(6, 182, 212, ${Math.random() * 0.2 + 0.1})`;
             ctx.fillRect(0,0, canvas.width, canvas.height);
        }

        particles.forEach(p => {
            p.x += p.vx + (isFilling ? (Math.random() - 0.5) * 5 : 0); 
            p.y += p.vy + (isFilling ? (Math.random() - 0.5) * 5 : 0);

            if (p.x < 0 || p.x > canvas.width) p.vx *= -1;
            if (p.y < 0 || p.y > canvas.height) p.vy *= -1;

            const dx = mouse.x - p.x;
            const dy = mouse.y - p.y;
            const dist = Math.sqrt(dx*dx + dy*dy);
            if (dist < 60) {
                p.x += dx * 0.05;
                p.y += dy * 0.05;
            }

            ctx.beginPath();
            ctx.fillStyle = isFilling ? '#fff' : 'rgba(255,255,255,0.7)';
            ctx.arc(p.x, p.y, isFilling ? 2 : 1.5, 0, Math.PI*2);
            ctx.fill();

            if (dist < 80 || isFilling) {
                particles.forEach(p2 => {
                    const d2 = Math.sqrt((p.x - p2.x)**2 + (p.y - p2.y)**2);
                    if (d2 < 40) {
                        ctx.beginPath();
                        ctx.strokeStyle = isFilling ? 'rgba(255,255,255,0.8)' : 'rgba(6, 182, 212, 0.4)';
                        ctx.lineWidth = isFilling ? 1.5 : 0.5;
                        ctx.moveTo(p.x, p.y);
                        ctx.lineTo(p2.x, p2.y);
                        ctx.stroke();
                    }
                });
            }
        });

        if (isFilling && Math.random() > 0.8) {
            ctx.beginPath();
            ctx.strokeStyle = 'rgba(255,255,255,0.9)';
            ctx.lineWidth = 2;
            ctx.moveTo(Math.random() * canvas.width, Math.random() * canvas.height);
            ctx.lineTo(Math.random() * canvas.width, Math.random() * canvas.height);
            ctx.stroke();
        }

        animationFrameId = requestAnimationFrame(animate);
    };

    animate();

    return () => {
        container.removeEventListener('mousemove', handleMouseMove);
        cancelAnimationFrame(animationFrameId);
    };
  }, [status]);

  return (
    <button
      ref={containerRef}
      onClick={onClick}
      disabled={disabled || status === 'filling'}
      className={`
        relative w-full h-14 overflow-hidden rounded-xl group transition-all duration-300
        border border-white/20 backdrop-blur-md bg-white/5 shadow-[0_4px_30px_rgba(0,0,0,0.1)]
        active:scale-95 hover:shadow-[0_0_20px_rgba(6,182,212,0.4)] hover:border-cyan-500/50
        ${status === 'filling' ? 'scale-105 border-white shadow-[0_0_50px_rgba(255,255,255,0.5)]' : ''}
      `}
      style={{
        transformStyle: 'preserve-3d',
        transform: disabled ? 'none' : 'perspective(500px) translateZ(0)',
      }}
    >
      <canvas ref={canvasRef} className="absolute inset-0 pointer-events-none" />
      <span className="relative z-10 font-bold uppercase tracking-wider text-sm flex items-center justify-center gap-2 text-white group-hover:text-cyan-200 transition-colors">
        {status === 'filling' ? 'PROCESSANDO...' : 'SOLICITAR PROPOSTA AGORA'}
        {status !== 'filling' && <Zap size={18} className="fill-current" />}
      </span>
    </button>
  );
};

/**
 * COMPONENTE DE EXPLOSÃO (OVERLAY)
 */
const GlassExplosion = ({ onComplete }) => {
    const canvasRef = useRef(null);

    useEffect(() => {
        const canvas = canvasRef.current;
        if (!canvas) return;
        const ctx = canvas.getContext('2d');
        if (!ctx) return;

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        const centerX = window.innerWidth / 2;
        const centerY = window.innerHeight / 2;
        const shards = [];

        for(let i=0; i<150; i++) {
            const angle = Math.random() * Math.PI * 2;
            const speed = Math.random() * 20 + 5;
            shards.push({
                x: centerX,
                y: centerY,
                vx: Math.cos(angle) * speed,
                vy: Math.sin(angle) * speed,
                r: Math.random() * 20 + 5, 
                size: Math.random() * 20 + 5,
                color: Math.random() > 0.5 ? 'rgba(255,255,255,0.9)' : 'rgba(6, 182, 212, 0.8)',
                rot: Math.random() * 360,
                vRot: (Math.random() - 0.5) * 20
            });
        }

        let opacity = 1;

        const animate = () => {
            if (!ctx) return;
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            if (opacity > 0) {
                ctx.fillStyle = `rgba(255, 255, 255, ${opacity * 0.2})`;
                ctx.fillRect(0,0,canvas.width, canvas.height);
            }

            shards.forEach(s => {
                s.x += s.vx;
                s.y += s.vy;
                s.vy += 0.2; // gravidade
                s.rot += s.vRot;
                s.vx *= 0.98; // atrito
                s.vy *= 0.98;

                ctx.save();
                ctx.translate(s.x, s.y);
                ctx.rotate(s.rot * Math.PI / 180);
                ctx.fillStyle = s.color;
                ctx.beginPath();
                ctx.moveTo(-s.r/2, -s.size/2);
                ctx.lineTo(s.r/2, -s.size/2);
                ctx.lineTo(0, s.size/2);
                ctx.fill();
                ctx.restore();
            });
            
            opacity -= 0.02;

            if (opacity > -1) { 
                requestAnimationFrame(animate);
            } else {
                onComplete();
            }
        };

        animate();

    }, [onComplete]);

    return (
        <canvas ref={canvasRef} className="fixed inset-0 z-50 pointer-events-none" />
    );
};

/* =================================================================================
   COMPONENTES PRINCIPAIS DO LAYOUT
   ================================================================================= */

/**
 * CARD DO FORMULÁRIO COM LÓGICA
 */
const FormCard = () => {
  const [formData, setFormData] = useState({
    nome: '',
    empresa: '',
    email: '',
    whatsapp: ''
  });
  const [status, setStatus] = useState('idle');
  const cardRef = useRef(null);
  const [rotation, setRotation] = useState({ x: 0, y: 0 });

  // Lógica do Tilt 3D
  const handleMouseMove = (e) => {
    if (!cardRef.current || status !== 'idle') return;
    const card = cardRef.current;
    const rect = card.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;
    
    const centerX = rect.width / 2;
    const centerY = rect.height / 2;
    
    const rotateX = ((y - centerY) / centerY) * -3;
    const rotateY = ((x - centerX) / centerX) * 3;

    setRotation({ x: rotateX, y: rotateY });
  };

  const handleMouseLeave = () => {
    setRotation({ x: 0, y: 0 });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (status !== 'idle') return;

    setStatus('filling');
    
    setTimeout(() => {
      setStatus('exploding');
      console.log("Dados capturados:", formData);
    }, 1500);
  };

  const handleChange = (e) => {
    if (e.target.name === 'whatsapp') {
      const val = e.target.value.replace(/\D/g, '');
      setFormData({ ...formData, [e.target.name]: val });
    } else {
      setFormData({ ...formData, [e.target.name]: e.target.value });
    }
  };

  if (status === 'exploding') {
      return <GlassExplosion onComplete={() => setStatus('success')} />;
  }

  if (status === 'success') {
    return (
      <div className="z-10 relative w-full max-w-md p-8 text-center animate-fade-in">
        <div className="bg-white/5 backdrop-blur-xl border border-white/10 rounded-2xl p-10 shadow-[0_0_100px_rgba(255,255,255,0.1)]">
          <div className="inline-flex items-center justify-center w-20 h-20 rounded-full bg-green-500/20 mb-6 animate-bounce">
            <CheckCircle className="w-10 h-10 text-green-400" />
          </div>
          <h2 className="text-3xl font-bold text-white mb-4">Sucesso!</h2>
          <p className="text-gray-300 text-lg leading-relaxed mb-6">
            Tudo certo, um de nossos especialistas vai entrar em contato com você!
          </p>
          <button 
            onClick={() => setStatus('idle')}
            className="text-white border-b border-white/30 pb-1 hover:border-white hover:text-cyan-300 transition-all"
          >
            Voltar ao início
          </button>
        </div>
      </div>
    );
  }

  return (
    <div 
      className={`z-10 relative w-full max-w-md perspective-1000 transition-opacity duration-500 ${status === 'filling' ? 'scale-105' : ''}`}
      onMouseMove={handleMouseMove}
      onMouseLeave={handleMouseLeave}
    >
      <div 
        ref={cardRef}
        style={{
          transform: `rotateX(${rotation.x}deg) rotateY(${rotation.y}deg)`,
          transition: 'transform 0.1s ease-out'
        }}
        className="bg-black/40 backdrop-blur-md border border-white/10 rounded-3xl p-8 shadow-2xl relative overflow-hidden group transition-all duration-500 hover:shadow-cyan-900/20"
      >
        <div className="absolute inset-0 bg-gradient-to-b from-white/5 to-transparent opacity-50 pointer-events-none" />
        
        {/* Cabeçalho do Card */}
        <div className="relative z-10 text-center mb-6">
          <div className="inline-flex items-center gap-2 mb-3 px-3 py-1 rounded-full bg-white/5 border border-white/10">
            <span className="w-1.5 h-1.5 rounded-full bg-white animate-pulse" />
            <span className="text-[10px] font-bold text-gray-300 uppercase tracking-widest">Oferta Exclusiva</span>
          </div>
          <h2 className="text-3xl md:text-4xl font-black text-white tracking-tight mb-2">
            ESCALA <span className="text-transparent bg-clip-text bg-gradient-to-r from-gray-200 to-gray-500">AUTOMÁTICA</span>
          </h2>
          <p className="text-gray-400 text-sm md:text-base leading-relaxed mb-4">
            Tenha seu funil de criativos pronto com<br/>
            <strong className="text-white">Vídeo IA + Imagem IA + Copy</strong>
          </p>

          {/* PREÇO EM DESTAQUE */}
          <div className="inline-flex items-center justify-center bg-white/5 px-8 py-3 rounded-xl border border-white/5 shadow-inner">
             <span className="text-3xl font-black text-white tracking-tighter drop-shadow-[0_0_10px_rgba(255,255,255,0.3)]">
                R$ 297,00
             </span>
          </div>
        </div>

        {/* Inputs */}
        <form onSubmit={handleSubmit} className="relative z-10 space-y-4">
          <InputGroup 
            icon={<User size={18} />}
            type="text"
            name="nome"
            placeholder="Nome Completo"
            value={formData.nome}
            onChange={handleChange}
            disabled={status !== 'idle'}
          />
          <InputGroup 
            icon={<Building2 size={18} />}
            type="text"
            name="empresa"
            placeholder="Sua Empresa"
            value={formData.empresa}
            onChange={handleChange}
            disabled={status !== 'idle'}
          />
          <InputGroup 
            icon={<Mail size={18} />}
            type="email"
            name="email"
            placeholder="E-mail Comercial"
            value={formData.email}
            onChange={handleChange}
            disabled={status !== 'idle'}
          />
          <InputGroup 
            icon={<Smartphone size={18} />}
            type="tel"
            name="whatsapp"
            placeholder="WhatsApp (DDD + Número)"
            value={formData.whatsapp}
            onChange={handleChange}
            disabled={status !== 'idle'}
          />

          <div className="mt-6">
            <NeuralButton 
                onClick={() => {}} 
                disabled={status !== 'idle'} 
                status={status}
            />
          </div>
        </form>

        <div className="mt-6 pt-4 border-t border-white/5 text-center relative z-10">
          <p className="text-xs text-gray-500 flex items-center justify-center gap-2">
            <span className="w-1.5 h-1.5 rounded-full bg-green-500"></span>
            Disponibilidade limitada
          </p>
        </div>
      </div>
    </div>
  );
};

const InputGroup = ({ icon, disabled, ...props }) => (
  <div className={`relative group ${disabled ? 'opacity-50' : ''}`}>
    <div className="absolute left-4 top-1/2 -translate-y-1/2 text-gray-500 group-focus-within:text-white transition-colors duration-300">
      {icon}
    </div>
    <input
      {...props}
      disabled={disabled}
      required
      className="w-full bg-white/5 border border-white/10 rounded-xl py-4 pl-12 pr-4 text-white placeholder-gray-600 outline-none focus:border-white/30 focus:bg-white/10 transition-all duration-300 font-medium disabled:cursor-not-allowed"
    />
  </div>
);

/**
 * FEATURES DESTAQUE (Monocromáticas/Sóbrias)
 */
const FeatureBadges = ({ onOpenVideo }) => {
  return (
    <div className="flex flex-wrap justify-center gap-4 mb-10 relative z-10 opacity-90">
      {/* Badge clicável que abre o Modal de Vídeo */}
      <Badge 
        icon={<PlayCircle size={16} />} 
        text="Vídeo IA"
        hoverText="Ver Exemplo"
        delay="0s" 
        onClick={onOpenVideo}
      />
      
      {/* Badge com link direto */}
      <Badge 
        icon={<Sparkles size={16} />} 
        text="Imagem IA" 
        hoverText="Ver Exemplo"
        delay="0.1s" 
        link="https://ibb.co/HSvL5XF"
      />

      <Badge icon={<Type size={16} />} text="Copywriting" delay="0.2s" />
    </div>
  );
};

const Badge = ({ icon, text, hoverText, delay, link, onClick }) => {
  const [isHovered, setIsHovered] = useState(false);
  const isInteractable = link || onClick;

  const content = (
    <div 
      className={`
        flex items-center gap-2 px-4 py-2 rounded-full bg-white/5 border border-white/10 backdrop-blur-md animate-float transition-all duration-300
        ${isInteractable ? 'hover:bg-white/10 hover:border-cyan-500/50 hover:shadow-[0_0_15px_rgba(6,182,212,0.3)] cursor-pointer group' : 'cursor-default'}
      `}
      style={{ animationDelay: delay }}
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      <div className={`text-gray-300 ${isInteractable && isHovered ? 'text-cyan-400' : ''} transition-colors`}>
        {icon}
      </div>
      <span className={`text-sm font-semibold ${isInteractable && isHovered ? 'text-cyan-300' : 'text-gray-200'} transition-colors`}>
        {isInteractable && isHovered && hoverText ? hoverText : text}
      </span>
      {isInteractable && !hoverText && <div className="ml-1 w-1.5 h-1.5 rounded-full bg-cyan-500 animate-pulse" />}
    </div>
  );

  if (link) {
    return (
      <a href={link} target="_blank" rel="noopener noreferrer" className="no-underline">
        {content}
      </a>
    );
  }

  if (onClick) {
    return (
      <div onClick={onClick}>
        {content}
      </div>
    );
  }

  return content;
};

/* =================================================================================
   APP PRINCIPAL
   ================================================================================= */
function App() {
  const [loading, setLoading] = useState(true);
  const [isVideoOpen, setIsVideoOpen] = useState(false);

  // Se estiver carregando, mostra o Preloader
  if (loading) {
    return <Preloader onComplete={() => setLoading(false)} />;
  }

  return (
    <div className="min-h-screen bg-black text-white font-sans selection:bg-white/30 overflow-x-hidden flex flex-col relative animate-fade-in">
      <TechBackground />
      
      {/* Modais */}
      <VideoModal isOpen={isVideoOpen} onClose={() => setIsVideoOpen(false)} />

      <main className="flex-grow flex flex-col items-center justify-center p-6 relative z-10 w-full max-w-7xl mx-auto">
        
        {/* HEADER / LOGO SECTION */}
        <div className="mb-8 text-center space-y-6 pt-10 md:pt-0">
          <div className="inline-block relative group cursor-default hover:scale-105 transition-transform duration-500">
             {/* Efeito Glow atrás da logo mais sutil */}
            <div className="absolute -inset-4 bg-white/5 rounded-full blur-xl opacity-0 group-hover:opacity-100 transition duration-1000"></div>
            <div className="relative px-8 py-4 bg-transparent rounded-xl flex items-center justify-center min-w-[200px]">
              <img 
                src="Logotipo Growth.png" 
                alt="Growth Level Logo" 
                className="h-12 md:h-16 object-contain filter brightness-100 contrast-125" 
                onError={(e) => {
                  e.currentTarget.style.display = 'none';
                  const sibling = e.currentTarget.nextElementSibling;
                  if (sibling) sibling.style.display = 'flex';
                }}
              />
              <div className="hidden items-center gap-3">
                <span className="font-mono font-bold tracking-[0.2em] text-xl text-white">
                  GROWTH LEVEL
                </span>
              </div>
            </div>
          </div>
          
          <h1 className="text-4xl md:text-6xl font-black text-white tracking-tighter leading-tight max-w-4xl mx-auto drop-shadow-2xl">
            AUMENTE SEU ROI COM A <br/>
            <span className="text-transparent bg-clip-text bg-gradient-to-r from-gray-100 via-gray-400 to-gray-100 animate-gradient-x">
              INTELIGÊNCIA ARTIFICIAL
            </span>
          </h1>
          <p className="text-gray-400 text-lg max-w-2xl mx-auto font-light">
            Não perca tempo criando criativos. Receba seu pack completo de alta conversão.
          </p>
        </div>

        <FeatureBadges 
            onOpenVideo={() => setIsVideoOpen(true)} 
        />

        {/* CONTAINER DO FORMULÁRIO */}
        <div className="w-full flex justify-center mb-12">
          <FormCard />
        </div>

        {/* SOCIAL PROOF / STATS - ICONS MONOCROMÁTICOS */}
        <div className="grid grid-cols-2 md:grid-cols-4 gap-6 md:gap-12 text-center w-full max-w-5xl border-t border-white/5 pt-10 pb-6 bg-gradient-to-b from-transparent to-black/80 rounded-t-3xl">
          <div className="flex flex-col items-center group cursor-default">
            <div className="mb-3 p-3 rounded-full bg-white/5 group-hover:bg-white/10 transition-colors">
              <Clock className="w-6 h-6 text-gray-300 group-hover:text-white transition-colors" />
            </div>
            <h4 className="font-bold text-white text-xl md:text-2xl">Até 48h</h4>
            <p className="text-gray-500 text-xs font-medium uppercase tracking-wider mt-1">Entrega Rápida</p>
          </div>
          
          <div className="flex flex-col items-center group cursor-default">
             <div className="mb-3 p-3 rounded-full bg-white/5 group-hover:bg-white/10 transition-colors">
              <TrendingUp className="w-6 h-6 text-gray-300 group-hover:text-white transition-colors" />
            </div>
            <h4 className="font-bold text-white text-xl md:text-2xl">+7 Milhões</h4>
            <p className="text-gray-500 text-xs font-medium uppercase tracking-wider mt-1">Gerados em Resultados</p>
          </div>
          
          <div className="flex flex-col items-center group cursor-default">
             <div className="mb-3 p-3 rounded-full bg-white/5 group-hover:bg-white/10 transition-colors">
              <Cpu className="w-6 h-6 text-gray-300 group-hover:text-white transition-colors" />
            </div>
            <h4 className="font-bold text-white text-xl md:text-2xl">Growth AI</h4>
            <p className="text-gray-500 text-xs font-medium uppercase tracking-wider mt-1">Tecnologia Atualizada</p>
          </div>
          
          <div className="flex flex-col items-center group cursor-default">
             <div className="mb-3 p-3 rounded-full bg-white/5 group-hover:bg-white/10 transition-colors">
              <Target className="w-6 h-6 text-gray-300 group-hover:text-white transition-colors" />
            </div>
            <h4 className="font-bold text-white text-xl md:text-2xl">ROI</h4>
            <p className="text-gray-500 text-xs font-medium uppercase tracking-wider mt-1">Foco em Resultado</p>
          </div>
        </div>

      </main>

      <footer className="relative z-10 w-full py-6 text-center text-gray-600 text-xs border-t border-white/5 bg-black/80 backdrop-blur-md">
        <p>© {new Date().getFullYear()} Growth Level Agency. Todos os direitos reservados.</p>
      </footer>

      <style>{`
        @keyframes float {
          0%, 100% { transform: translateY(0); }
          50% { transform: translateY(-8px); }
        }
        .animate-float {
          animation: float 5s ease-in-out infinite;
        }
        .perspective-1000 {
          perspective: 1000px;
        }
        /* Custom Scrollbar */
        ::-webkit-scrollbar {
          width: 6px;
        }
        ::-webkit-scrollbar-track {
          background: #000;
        }
        ::-webkit-scrollbar-thumb {
          background: #333;
          border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
          background: #555;
        }
        .animate-gradient-x {
          background-size: 200% 200%;
          animation: gradient-x 6s ease infinite;
        }
        @keyframes gradient-x {
          0% { background-position: 0% 50% }
          50% { background-position: 100% 50% }
          100% { background-position: 0% 50% }
        }
        .animate-fade-in {
          animation: fadeIn 0.5s ease-out forwards;
        }
        @keyframes fadeIn {
          from { opacity: 0; transform: translateY(10px); }
          to { opacity: 1; transform: translateY(0); }
        }
      `}</style>
    </div>
  );
}

export default App;
