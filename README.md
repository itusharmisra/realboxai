# realboxai


import React, { useState, useEffect } from 'react';
import { Bot, Check, Database, TrendingUp, Speaker, ConciergeBell, Contact2, ScanSearch, CalendarClock, Gift, MonitorSmartphone, UserSquare, User, Mail, PartyPopper, Phone, Home, ArrowDown } from 'lucide-react';
import { Lead } from '@/entities/Lead';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
// Removed: import { Link } from 'react-router-dom'; // Assuming react-router-dom for Link component

const AI_HELPERS = [
  {
    title: "Marketing Maverick",
    description: "Generates social media posts, ads, and email campaigns to attract new leads.",
    icon: Speaker,
    gradient: "from-blue-500 to-indigo-500",
  },
  {
    title: "Lead Concierge",
    description: "Instantly engages new leads via SMS and email, qualifying them 24/7.",
    icon: ConciergeBell,
    gradient: "from-purple-500 to-pink-500",
  },
  {
    title: "Contact Genie",
    description: "Manages your contact database, enriching profiles and suggesting follow-ups.",
    icon: Contact2,
    gradient: "from-green-500 to-emerald-500",
  },
  {
    title: "Market Scanner",
    description: "Analyzes MLS data to find hot properties and market trends for your clients.",
    icon: ScanSearch,
    gradient: "from-yellow-500 to-orange-500",
  },
  {
    title: "Showing Scheduler",
    description: "Coordinates with clients and agents to automatically book property viewings.",
    icon: CalendarClock,
    gradient: "from-rose-500 to-red-500",
  },
  {
    title: "Client Closer",
    description: "Nurtures long-term leads with personalized updates and check-ins.",
    icon: Gift,
    gradient: "from-teal-500 to-cyan-500",
  },
  {
    title: "Listing Promoter",
    description: "Creates stunning single-property websites and marketing materials automatically.",
    icon: MonitorSmartphone,
    gradient: "from-fuchsia-500 to-purple-600",
  },
  {
    title: "Agent Assistant",
    description: "Handles your administrative tasks, freeing you up to focus on clients.",
    icon: UserSquare,
    gradient: "from-sky-500 to-blue-600",
  },
];

export default function HomePage() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    phone: ''
  });
  const [loading, setLoading] = useState(false);
  const [submitted, setSubmitted] = useState(false);
  const [showMobileCta, setShowMobileCta] = useState(false);

  // Removed: Helper function to create page URLs based on assumed routing
  // Removed: const createPageUrl = (pageName) => {
  // Removed:   switch (pageName) {
  // Removed:     case 'Login':
  // Removed:       return '/sign-in'; // Or '/login', depending on actual Clerk setup
  // Removed:     // Add other page names as needed
  // Removed:     default:
  // Removed:       return '/';
  // Removed:   }
  // Removed: };

  useEffect(() => {
    // Add Google Analytics/Ads tracking script
    const script1 = document.createElement('script');
    script1.async = true;
    script1.src = 'https://www.googletagmanager.com/gtag/js?id=AW-17269807032';
    document.head.appendChild(script1);

    const script2 = document.createElement('script');
    script2.innerHTML = `
      window.dataLayer = window.dataLayer || [];
      function gtag(){dataLayer.push(arguments);}
      gtag('js', new Date());
      gtag('config', 'AW-17269807032');
    `;
    document.head.appendChild(script2);

    // Facebook Pixel
    const fbScript = document.createElement('script');
    fbScript.innerHTML = `
      !function(f,b,e,v,n,t,s)
      {if(f.fbq)return;n=f.fbq=function(){n.callMethod?
      n.callMethod.apply(n,arguments):n.queue.push(arguments)};
      if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version='2.0';
      n.queue=[];t=b.createElement(e);t.async=!0;
      t.src='https://connect.facebook.net/en_US/fbevents.js';
      s=b.getElementsByTagName(e)[0];
      s.parentNode.insertBefore(t,s)}(window, document,'script',
      'https://connect.facebook.net/en_US/fbevents.js');
      fbq('init', 'YOUR_PIXEL_ID');
      fbq('track', 'PageView');
    `;
    document.head.appendChild(fbScript);

    // Check for submission status in URL on initial load to persist state across reloads
    const urlParams = new URLSearchParams(window.location.search);
    if (urlParams.get('submission') === 'success') {
      setSubmitted(true);
    }

    const handleScroll = () => {
      if (window.scrollY > 200) {
        setShowMobileCta(true);
      } else {
        setShowMobileCta(false);
      }
    };
    window.addEventListener('scroll', handleScroll);
    return () => {
      window.removeEventListener('scroll', handleScroll);
      // Clean up the dynamically added scripts if component unmounts
      // In most SPA scenarios, these scripts persist, but it's good practice.
      // Check if element exists before removing to prevent errors if already removed by another effect/unmount.
      if (document.head.contains(script1)) document.head.removeChild(script1);
      if (document.head.contains(script2)) document.head.removeChild(script2);
      if (document.head.contains(fbScript)) document.head.removeChild(fbScript);
    };
  }, []);

  const handleInputChange = (field, value) => {
    setFormData(prev => ({
      ...prev,
      [field]: value
    }));
  };

  const handleEmailCapture = async () => {
    if (!formData.email) {
      document.querySelector('input[type="email"]').focus();
      return;
    }
    
    try {
      // Store email immediately as a partial lead
      await Lead.create({
        name: 'Email Capture',
        email: formData.email,
        phone: 'Not provided'
      });
      
      // Fire conversion tracking events for email capture
      if (window.gtag) {
        window.gtag('event', 'conversion', {
          'send_to': 'AW-17269807032/EMAIL_CAPTURE_LABEL', // Replace with your actual conversion label
          'value': 0.5,
          'currency': 'USD'
        });
      }
      
      if (window.fbq) {
        window.fbq('track', 'Lead', {
          content_name: 'RealBox Email Capture',
          content_category: 'Real Estate Software',
          value: 25.0,
          currency: 'USD'
        });
      }
    } catch (error) {
      console.error("Failed to capture email:", error);
    }
    
    // Scroll to full form
    document.getElementById('signup-form').scrollIntoView({ behavior: 'smooth' });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!formData.name || !formData.email || !formData.phone) return;
    setLoading(true);
    try {
      await Lead.create(formData);
      setSubmitted(true);
      
      // Fire conversion tracking events
      if (window.gtag) {
        window.gtag('event', 'conversion', {
          'send_to': 'AW-17269807032/CONVERSION_LABEL', // Replace with your actual conversion label
          'value': 1.0,
          'currency': 'USD'
        });
      }
      
      if (window.fbq) {
        window.fbq('track', 'Lead', {
          content_name: 'RealBox AI Signup',
          content_category: 'Real Estate Software',
          value: 49.99,
          currency: 'USD'
        });
      }
      
      // Change URL for tracking purposes without reloading the page
      window.history.pushState({ submitted: true }, "Submission Successful", "?submission=success");
    } catch (error) {
      console.error("Failed to submit lead:", error);
      // Optionally, show an error message to the user
    } finally {
      setLoading(false);
    }
  };

  const scrollToSignup = () => {
    document.getElementById('signup-form').scrollIntoView({ behavior: 'smooth' });
  };

  const handleGetStarted = () => {
    // If form has been submitted, redirect to signed up page
    if (submitted) {
      window.location.href = 'https://realboxai.com/signedup';
    } else {
      // Otherwise scroll to signup form
      document.getElementById('signup-form').scrollIntoView({ behavior: 'smooth' });
    }
  };

  const scrollToFeatures = () => {
    document.getElementById('features').scrollIntoView({ behavior: 'smooth' });
  };

  return (
    <div className="bg-[#f8f9fc] text-gray-800 font-sans min-h-screen relative overflow-x-hidden">
      <style>{`
        @keyframes tilt {
          0%, 50%, 100% {
            transform: rotate(0deg);
          }
          25% {
            transform: rotate(0.5deg);
          }
          75% {
            transform: rotate(-0.5deg);
          }
        }
        .animate-tilt {
          animation: tilt 10s infinite linear;
        }
        @keyframes slide-in-bottom {
            0% { transform: translateY(100%); opacity: 0; }
            100% { transform: translateY(0); opacity: 1; }
        }
        .animate-slide-in-bottom {
            animation: slide-in-bottom 0.3s ease-out forwards;
        }
      `}</style>
      {/* Animated Background Blobs */}
      <div className="absolute top-0 left-0 w-full h-full overflow-hidden z-0">
        <div className="absolute -top-64 -left-64 w-[40rem] h-[40rem] bg-gradient-to-r from-blue-200 via-purple-200 to-pink-200 rounded-full mix-blend-multiply filter blur-3xl opacity-40 animate-blob"></div>
        <div className="absolute -bottom-64 -right-64 w-[40rem] h-[40rem] bg-gradient-to-r from-cyan-200 via-teal-200 to-green-200 rounded-full mix-blend-multiply filter blur-3xl opacity-40 animate-blob animation-delay-2000"></div>
        <div className="absolute -bottom-32 -left-1/4 w-[40rem] h-[40rem] bg-gradient-to-r from-rose-200 to-orange-200 rounded-full mix-blend-multiply filter blur-3xl opacity-30 animate-blob animation-delay-4000"></div>
      </div>
      
      <div className="relative z-10 pb-24 md:pb-0">
        {/* Header */}
        <header className="fixed top-0 left-0 right-0 z-50 bg-white/80 backdrop-blur-xl border-b border-white/30">
          <div className="container mx-auto px-6 py-4 flex justify-between items-center">
            <div className="flex items-center gap-2">
              <div className="w-8 h-8 bg-gradient-to-r from-blue-600 to-purple-600 rounded-lg flex items-center justify-center shadow-lg shadow-blue-500/20">
                <Home className="w-5 h-5 text-white" />
              </div>
              <span className="text-xl font-bold bg-gradient-to-r from-gray-900 to-gray-700 bg-clip-text text-transparent">RealBox</span>
            </div>
            <nav className="hidden md:flex items-center gap-6 text-sm font-medium text-gray-600">
              <a href="#features" className="hover:text-blue-600 transition-colors">Features</a>
              <a href="#pricing" className="hover:text-purple-600 transition-colors">Pricing</a>
            </nav>
            <div className="flex items-center gap-3">
              <a 
                href="/login"
                className="bg-white text-gray-700 border-2 border-gray-200 px-6 py-2 rounded-xl text-sm font-semibold hover:border-gray-300 hover:bg-gray-50 transition-all duration-300"
              >
                Login
              </a>
              <button 
                onClick={handleGetStarted}
                className="bg-gradient-to-r from-blue-600 to-purple-600 text-white px-6 py-2 rounded-xl text-sm font-semibold hover:shadow-lg hover:shadow-blue-500/25 transition-all duration-300 transform hover:scale-105"
              >
                Start Free Trial
              </button>
            </div>
          </div>
        </header>

        {/* Hero Section - Optimized for Ad Traffic */}
        <section className="pt-24 md:pt-40 pb-8 md:pb-16">
          <div className="container mx-auto px-6">
            <div className="text-center mb-6 md:mb-8">
              <div className="inline-flex items-center gap-2 bg-gradient-to-r from-blue-100 to-purple-100 text-blue-800 px-4 py-2 rounded-full text-sm font-semibold mb-4 md:mb-6">
                <span className="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                Limited Time: 14-Day Free Trial
              </div>
              
              <h1 className="text-3xl md:text-6xl font-extrabold tracking-tighter leading-tight md:leading-tight mb-4 md:mb-6">
                <span className="bg-gradient-to-r from-blue-600 via-purple-600 to-cyan-600 bg-clip-text text-transparent">
                  Real Estate Agents:
                </span>
                <br />
                <span className="bg-gradient-to-r from-blue-600 via-purple-600 to-cyan-600 bg-clip-text text-transparent">
                  Get 10X More Leads
                </span>
                <br />
                <span className="text-gray-900">With AI Automation</span>
              </h1>
              <p className="mt-3 md:mt-4 max-w-2xl mx-auto text-lg md:text-xl text-gray-700 leading-relaxed font-medium mb-6 md:mb-8">
                Real estate agents using RealBox AI are generating <strong>300% more qualified leads</strong> while working 50% less. Join 1,000+ agents already automating their success.
              </p>
              
              {/* Big Bold CTA Above the Fold */}
              <div className="max-w-lg mx-auto mb-6 md:mb-8">
                <div className="flex flex-col gap-3 md:gap-4">
                  <div className="relative">
                    <Mail className="absolute left-4 top-1/2 -translate-y-1/2 w-5 md:w-6 h-5 md:h-6 text-gray-400" />
                    <Input
                      type="email"
                      placeholder="Enter your email to get started"
                      value={formData.email}
                      onChange={(e) => handleInputChange('email', e.target.value)}
                      className="w-full text-base md:text-lg h-12 md:h-16 pl-12 md:pl-14 pr-4 rounded-2xl border-2 border-gray-200 focus:ring-4 focus:ring-blue-400/20 focus:border-blue-400 shadow-lg"
                    />
                  </div>
                  <button 
                    onClick={handleEmailCapture}
                    className="bg-gradient-to-r from-blue-600 to-purple-600 text-white font-black text-sm md:text-xl py-3 md:py-6 px-3 md:px-8 rounded-2xl shadow-2xl hover:shadow-2xl hover:shadow-blue-500/40 transition-all duration-300 transform hover:scale-105 uppercase tracking-wide"
                  >
                    🚀 Get My Free AI Co-Agent Now
                  </button>
                </div>
                <div className="flex items-center justify-center gap-2 text-xs md:text-sm text-gray-600 mt-3 md:mt-4">
                  <Check className="w-3 md:w-4 h-3 md:h-4 text-green-500" />
                  <span>No credit card required • 14-day free trial</span>
                </div>
              </div>

              {/* Social Proof */}
              <div className="mt-8 flex flex-col sm:flex-row items-center justify-center gap-6 text-sm text-gray-600">
                <div className="flex items-center gap-2">
                  <div className="flex -space-x-2">
                    <div className="w-8 h-8 rounded-full border-2 border-white bg-gradient-to-br from-blue-400 to-blue-600 flex items-center justify-center text-white text-xs font-semibold">
                      JD
                    </div>
                    <div className="w-8 h-8 rounded-full border-2 border-white bg-gradient-to-br from-green-400 to-green-600 flex items-center justify-center text-white text-xs font-semibold">
                      SM
                    </div>
                    <div className="w-8 h-8 rounded-full border-2 border-white bg-gradient-to-br from-purple-400 to-purple-600 flex items-center justify-center text-white text-xs font-semibold">
                      AL
                    </div>
                  </div>
                  <span>1,000+ agents trust RealBox</span>
                </div>
                <div className="flex items-center gap-1">
                  {[...Array(5)].map((_, i) => (
                    <span key={i} className="text-yellow-400">★</span>
                  ))}
                  <span className="ml-1">4.9/5 rating</span>
                </div>
              </div>
            </div>

            {/* Quick Benefits - Above the fold */}
            <div className="grid md:grid-cols-3 gap-6 max-w-4xl mx-auto mt-12">
              <div className="bg-white/70 backdrop-blur-lg p-6 rounded-2xl border border-white/50 shadow-lg text-center">
                <div className="w-12 h-12 bg-gradient-to-r from-green-500 to-emerald-500 rounded-lg flex items-center justify-center mx-auto mb-4">
                  <TrendingUp className="w-6 h-6 text-white" />
                </div>
                <h3 className="font-bold text-lg text-gray-900 mb-2">300% More Leads</h3>
                <p className="text-sm text-gray-600">Automated lead generation that works 24/7</p>
              </div>
              
              <div className="bg-white/70 backdrop-blur-lg p-6 rounded-2xl border border-white/50 shadow-lg text-center">
                <div className="w-12 h-12 bg-gradient-to-r from-blue-500 to-indigo-500 rounded-lg flex items-center justify-center mx-auto mb-4">
                  <CalendarClock className="w-6 h-6 text-white" />
                </div>
                <h3 className="font-bold text-lg text-gray-900 mb-2">Save 20 Hours/Week</h3>
                <p className="text-sm text-gray-600">AI handles marketing, follow-ups, and scheduling</p>
              </div>
              
              <div className="bg-white/70 backdrop-blur-lg p-6 rounded-2xl border border-white/50 shadow-lg text-center">
                <div className="w-12 h-12 bg-gradient-to-r from-purple-500 to-pink-500 rounded-lg flex items-center justify-center mx-auto mb-4">
                  <Bot className="w-6 h-6 text-white" />
                </div>
                <h3 className="font-bold text-lg text-gray-900 mb-2">8 AI Helpers</h3>
                <p className="text-sm text-gray-600">Specialized AI for every aspect of your business</p>
              </div>
            </div>
          </div>
        </section>

        {/* AI Helpers Section */}
        <section className="container mx-auto px-6 py-20 md:py-28" id="features">
          <div className="text-center mb-16">
            <h2 className="text-3xl md:text-4xl font-bold tracking-tight mb-4">
              <span className="bg-gradient-to-r from-indigo-600 to-purple-600 bg-clip-text text-transparent">Meet Your AI Helpers</span>
            </h2>
            <p className="max-w-2xl mx-auto text-lg text-gray-700">
              A dedicated AI helper for every critical task in your real estate business.
            </p>
          </div>
          <div className="grid md:grid-cols-2 lg:grid-cols-4 gap-8">
            {AI_HELPERS.map((agent) => (
              <div key={agent.title} className="group bg-white/60 backdrop-blur-lg p-6 rounded-3xl border border-white/50 transition-all duration-300 hover:bg-white/90 hover:shadow-2xl hover:shadow-gray-500/10 hover:-translate-y-2">
                <div className="relative">
                  <div className={`flex items-center justify-center w-14 h-14 bg-gradient-to-r ${agent.gradient} rounded-2xl shadow-lg mb-5`}>
                    <agent.icon className="w-7 h-7 text-white" />
                  </div>
                  <h3 className="text-lg font-semibold text-gray-900 mb-2">{agent.title}</h3>
                  <p className="text-sm text-gray-600 leading-relaxed">{agent.description}</p>
                </div>
              </div>
            ))}
          </div>
        </section>

        {/* How It Works Section */}
        <section className="container mx-auto px-6 py-20 md:py-28">
            <div className="relative bg-white/50 backdrop-blur-md rounded-3xl p-10 md:p-16">
              <div className="text-center mb-12">
                <h2 className="text-3xl md:text-4xl font-bold tracking-tight mb-4">
                  <span className="bg-gradient-to-r from-cyan-600 to-blue-600 bg-clip-text text-transparent">Get Started in 3 Simple Steps</span>
                </h2>
                <p className="max-w-2xl mx-auto text-lg text-gray-700">
                  Go from overwhelmed to automated in less time than it takes to brew coffee.
                </p>
              </div>
              <div className="grid md:grid-cols-3 gap-10">
                {[
                  { icon: Database, title: "Connect Your Data", description: "Securely link your MLS, contacts, and social accounts in minutes. Your data provides the fuel.", gradient: "from-emerald-500 to-teal-500" },
                  { icon: Bot, title: "Activate Your Helpers", description: "Choose which AI helpers you want to deploy. Customize their tone and tasks to match your brand.", gradient: "from-blue-500 to-indigo-500" },
                  { icon: TrendingUp, title: "Watch Your Business Grow", description: "Your AI team works 24/7, generating leads, engaging clients, and streamlining your operations.", gradient: "from-purple-500 to-pink-500" }
                ].map((step) => (
                  <div key={step.title} className="text-center">
                    <div className={`flex items-center justify-center w-20 h-20 mx-auto bg-gradient-to-r ${step.gradient} rounded-3xl shadow-lg mb-6 transform transition-transform group-hover:scale-110`}>
                      <step.icon className="w-10 h-10 text-white" />
                    </div>
                    <h3 className="text-xl font-semibold text-gray-900 mb-3">{step.title}</h3>
                    <p className="text-gray-600 leading-relaxed">{step.description}</p>
                  </div>
                ))}
              </div>
            </div>
        </section>

        {/* Connect Your Data Section */}
        <section className="container mx-auto px-6 py-16 md:py-20">
          <div className="text-center mb-12">
            <h2 className="text-2xl md:text-3xl font-bold tracking-tight text-gray-900 mb-4">
              Connect Your Data
            </h2>
            <p className="max-w-2xl mx-auto text-lg text-gray-600">
              Seamlessly integrate with the tools you already use
            </p>
          </div>
          
          <div className="grid grid-cols-2 md:grid-cols-4 lg:grid-cols-6 gap-8 items-center justify-items-center">
            {/* MLS */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://images.unsplash.com/photo-1560472354-b33ff0c44a43?w=48&h=48&fit=crop&crop=center" alt="MLS Logo" className="w-12 h-12 rounded-lg object-cover" />
              <span className="text-sm font-medium text-gray-700">MLS</span>
            </div>

            {/* WhatsApp */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://upload.wikimedia.org/wikipedia/commons/6/6b/WhatsApp.svg" alt="WhatsApp Logo" className="w-12 h-12 rounded-lg object-contain" />
              <span className="text-sm font-medium text-gray-700">WhatsApp</span>
            </div>

            {/* Follow Up Boss */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://logo.clearbit.com/followupboss.com" alt="Follow Up Boss Logo" className="w-12 h-12 rounded-lg object-contain" />
              <span className="text-sm font-medium text-gray-700">Follow Up Boss</span>
            </div>

            {/* Sierra Interactive */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://logo.clearbit.com/sierrainteractive.com" alt="Sierra Interactive Logo" className="w-12 h-12 rounded-lg object-contain" />
              <span className="text-sm font-medium text-gray-700">Sierra Interactive</span>
            </div>

            {/* Salesforce */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://logo.clearbit.com/salesforce.com" alt="Salesforce Logo" className="w-12 h-12 rounded-lg object-contain" />
              <span className="text-sm font-medium text-gray-700">Salesforce</span>
            </div>

            {/* HubSpot */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://logo.clearbit.com/hubspot.com" alt="HubSpot Logo" className="w-12 h-12 rounded-lg object-contain" />
              <span className="text-sm font-medium text-gray-700">HubSpot</span>
            </div>

            {/* Facebook */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://logo.clearbit.com/facebook.com" alt="Facebook Logo" className="w-12 h-12 rounded-lg object-contain" />
              <span className="text-sm font-medium text-gray-700">Facebook</span>
            </div>

            {/* Instagram */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://logo.clearbit.com/instagram.com" alt="Instagram Logo" className="w-12 h-12 rounded-lg object-contain" />
              <span className="text-sm font-medium text-gray-700">Instagram</span>
            </div>

            {/* TikTok */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <div className="w-12 h-12 bg-gradient-to-br from-gray-800 to-gray-900 rounded-lg flex items-center justify-center text-white font-bold text-xs">
                TT
              </div>
              <span className="text-sm font-medium text-gray-700">TikTok</span>
            </div>

            {/* Gmail */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://logo.clearbit.com/gmail.com" alt="Gmail Logo" className="w-12 h-12 rounded-lg object-contain" />
              <span className="text-sm font-medium text-gray-700">Gmail</span>
            </div>

            {/* Outlook */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <img src="https://logo.clearbit.com/outlook.com" alt="Outlook Logo" className="w-12 h-12 rounded-lg object-contain" />
              <span className="text-sm font-medium text-gray-700">Outlook</span>
            </div>

            {/* And More */}
            <div className="flex flex-col items-center justify-center gap-3 p-4 rounded-xl bg-white/60 backdrop-blur-sm hover:bg-white/80 transition-all duration-300 w-full h-full text-center">
              <div className="w-12 h-12 bg-gradient-to-br from-gray-400 to-gray-500 rounded-lg flex items-center justify-center text-white font-bold text-xs">
                +50
              </div>
              <span className="text-sm font-medium text-gray-700">And More</span>
            </div>
          </div>
        </section>

        {/* Signup Form Section - Optimized */}
        <section id="signup-form" className="container mx-auto px-6 py-16 md:py-20">
          <div className="max-w-2xl mx-auto">
            {/* Urgency Banner */}
            <div className="bg-gradient-to-r from-red-500 to-pink-500 text-white text-center py-3 px-6 rounded-t-2xl">
              <p className="font-semibold">⚡ Limited Time Offer - Free 14-Day Trial</p>
            </div>
            
            <div className="bg-white/90 backdrop-blur-lg border border-white/50 rounded-b-2xl p-8 shadow-xl">
              <div className="text-center mb-8">
                <h2 className="text-3xl md:text-4xl font-bold tracking-tight mb-4">
                  <span className="bg-gradient-to-r from-blue-600 to-purple-600 bg-clip-text text-transparent">Start Your Free Trial</span>
                </h2>
                <p className="text-lg text-gray-700 mb-4">
                  Join 1,000+ real estate agents who increased their income by 200%+ with AI automation.
                </p>
                
                {/* Benefits List */}
                <div className="grid md:grid-cols-2 gap-3 text-left mb-6 text-sm">
                  {[
                    "✅ 8 AI helpers working 24/7 for you",
                    "✅ Auto-generate social media content",
                    "✅ Instant lead matching & notifications", 
                    "✅ Automated email & SMS campaigns",
                    "✅ Smart appointment scheduling",
                    "✅ Professional listing websites"
                  ].map((benefit, i) => (
                    <div key={i} className="flex items-center gap-2 text-gray-700">
                      <span>{benefit}</span>
                    </div>
                  ))}
                </div>
              </div>
              
              {submitted ? (
                <div className="bg-gradient-to-r from-green-50 to-emerald-50 border border-green-200 rounded-2xl p-8 flex flex-col items-center gap-4 shadow-lg">
                   <div className="w-16 h-16 bg-gradient-to-br from-green-400 to-emerald-500 rounded-full flex items-center justify-center text-white">
                      <PartyPopper className="w-8 h-8" />
                   </div>
                   <h3 className="text-2xl font-bold text-gray-900">You're on the list!</h3>
                   <p className="text-gray-600 text-center">Thanks for signing up. We'll be in touch soon with next steps.</p>
                   <button 
                     onClick={handleGetStarted}
                     className="bg-gradient-to-r from-blue-600 to-purple-600 text-white px-8 py-3 rounded-xl font-semibold hover:shadow-lg transition-all duration-300"
                   >
                     Access Your Account →
                   </button>
                </div>
              ) : (
                <form onSubmit={handleSubmit} className="space-y-4">
                  <div className="grid md:grid-cols-2 gap-4">
                    <div className="relative">
                      <User className="absolute left-4 top-1/2 -translate-y-1/2 w-5 h-5 text-gray-400" />
                      <Input
                        type="text"
                        placeholder="Full Name"
                        value={formData.name}
                        onChange={(e) => handleInputChange('name', e.target.value)}
                        required
                        className="w-full text-base h-auto py-4 pl-12 pr-4 rounded-xl border-gray-200 focus:ring-2 focus:ring-blue-400"
                        disabled={loading}
                      />
                    </div>
                    <div className="relative">
                      <Phone className="absolute left-4 top-1/2 -translate-y-1/2 w-5 h-5 text-gray-400" />
                      <Input
                        type="tel"
                        placeholder="Phone Number"
                        value={formData.phone}
                        onChange={(e) => handleInputChange('phone', e.target.value)}
                        required
                        className="w-full text-base h-auto py-4 pl-12 pr-4 rounded-xl border-gray-200 focus:ring-2 focus:ring-blue-400"
                        disabled={loading}
                      />
                    </div>
                  </div>
                  <div className="relative">
                    <Mail className="absolute left-4 top-1/2 -translate-y-1/2 w-5 h-5 text-gray-400" />
                    <Input
                      type="email"
                      placeholder="Email Address"
                      value={formData.email}
                      onChange={(e) => handleInputChange('email', e.target.value)}
                      required
                      className="w-full text-base h-auto py-4 pl-12 pr-4 rounded-xl border-gray-200 focus:ring-2 focus:ring-blue-400"
                      disabled={loading}
                    />
                  </div>
                  <Button
                    type="submit"
                    className="w-full bg-gradient-to-r from-blue-600 to-purple-600 text-white font-bold text-lg py-5 px-8 rounded-xl hover:shadow-lg hover:shadow-blue-500/30 transition-all duration-300 transform hover:scale-105"
                    disabled={loading}
                  >
                    {loading ? 'Starting Your Trial...' : 'Start My Free Trial Now →'}
                  </Button>
                  
                  <div className="text-center text-xs text-gray-500 mt-4">
                    <p>By signing up, you agree to our Terms of Service and Privacy Policy.</p>
                    <p className="mt-1">💳 No credit card required • ⏰ 14-day free trial • ❌ Cancel anytime</p>
                  </div>
                </form>
              )}
            </div>
          </div>
        </section>

        {/* Pricing Section */}
        <section className="container mx-auto px-6 py-20 md:py-28" id="pricing">
          <div className="text-center mb-16">
            <h2 className="text-3xl md:text-4xl font-bold tracking-tight mb-4">
              <span className="bg-gradient-to-r from-rose-600 to-pink-600 bg-clip-text text-transparent">Simple, Transparent Pricing</span>
            </h2>
            <p className="max-w-2xl mx-auto text-lg text-gray-700">
              Choose the plan that's right for your business. No hidden fees.
            </p>
          </div>
          <div className="flex justify-center items-stretch gap-8 flex-wrap">
            {[
              { name: 'Pro Agent', price: '49.99', description: 'For the individual agent ready to supercharge their productivity.', features: ['All 8 AI Helpers', 'Unlimited Social Posts', 'Up to 2,500 Contacts', 'Standard Email & Chat Support'], cta: 'Start 14-Day Free Trial', isPopular: true, gradient: 'from-blue-500 to-purple-600', shadow: 'shadow-blue-500/20' },
              { name: 'Team & Brokerage', price: 'Custom', description: 'For teams and brokerages who need advanced collaboration and branding.', features: ['Everything in Pro', 'Team Dashboards & Reporting', 'Custom Branding', 'Dedicated Account Manager'], cta: 'Contact Sales', isPopular: false, gradient: 'from-gray-700 to-gray-900', shadow: 'shadow-gray-500/20' }
            ].map((tier) => (
              <div key={tier.name} className={`relative w-full max-w-sm bg-white/80 backdrop-blur-lg border rounded-3xl p-8 flex flex-col transition-all duration-300 hover:shadow-2xl hover:scale-105 ${tier.isPopular ? `border-blue-200 shadow-xl ${tier.shadow}` : 'border-gray-200'}`}>
                {tier.isPopular && (
                  <div className="absolute -top-4 left-1/2 -translate-x-1/2">
                    <div className="bg-gradient-to-r from-blue-600 to-purple-600 text-white text-xs font-semibold px-6 py-2 rounded-full uppercase shadow-lg">
                      Most Popular
                    </div>
                  </div>
                )}
                <h3 className="text-2xl font-semibold text-gray-900 mb-2">{tier.name}</h3>
                <p className="text-gray-600 mb-6 h-12">{tier.description}</p>
                <div className="mb-8 text-center">
                  {tier.price === 'Custom' ? ( <p className="text-5xl font-bold tracking-tight text-gray-900">{tier.price}</p> ) : ( 
                    <>
                      <p className="text-5xl font-bold tracking-tight text-gray-900">${tier.price}<span className="text-lg font-medium text-gray-500">/month</span></p>
                      <p className="mt-2 text-sm font-semibold text-blue-600">Includes a 14-day free trial</p>
                      <p className="mt-1 text-xs text-gray-500">No credit card required</p>
                    </>
                  )}
                </div>
                <ul className="space-y-4 text-gray-600 flex-grow mb-8">
                  {tier.features.map((feature) => (
                    <li key={feature} className="flex items-start">
                      <div className="w-5 h-5 bg-gradient-to-r from-green-400 to-emerald-500 rounded-full flex items-center justify-center mr-3 flex-shrink-0 mt-0.5"><Check className="w-3 h-3 text-white" /></div>
                      <span>{feature}</span>
                    </li>
                  ))}
                </ul>
                <button className={`w-full font-semibold text-base py-5 rounded-2xl transition-all duration-300 transform hover:scale-105 shadow-lg hover:shadow-xl text-white bg-gradient-to-r ${tier.gradient} ${tier.shadow}`}>
                  {tier.cta}
                </button>
              </div>
            ))}
          </div>
        </section>

        {/* CTA Section */}
        <section className="container mx-auto px-6 py-20 md:py-28">
          <div className="relative text-center bg-gradient-to-r from-gray-900 via-blue-900 to-purple-900 rounded-3xl px-8 py-16 overflow-hidden">
            <div className="absolute inset-0 bg-gradient-to-r from-blue-600/20 to-purple-600/20 mix-blend-color-dodge"></div>
            <div className="absolute top-0 left-0 w-full h-full bg-grid-white/[0.05]"></div>
            <div className="relative z-10">
                <h2 className="text-3xl md:text-4xl font-bold tracking-tight text-white mb-4">Ready to Reclaim Your Time?</h2>
                <p className="max-w-2xl mx-auto text-lg text-gray-300 mb-8">
                  Stop juggling tasks and start closing deals. Activate your AI team today and see the difference.
                </p>
                <button 
                  onClick={handleGetStarted}
                  className="bg-gradient-to-r from-white to-gray-100 hover:from-gray-100 hover:to-white text-gray-900 font-semibold text-base py-5 px-8 rounded-2xl shadow-xl hover:shadow-2xl transition-all duration-300 transform hover:scale-105"
                >
                  Start Your 14-Day Free Trial
                </button>
                <p className="mt-4 text-sm text-gray-400">No credit card required to start.</p>
            </div>
          </div>
        </section>

        {/* Footer */}
        <footer className="bg-transparent">
          <div className="container mx-auto px-6 py-8">
              <div className="flex flex-col md:flex-row justify-between items-center">
                  <div className="flex items-center gap-2 mb-4 md:mb-0">
                      <div className="w-8 h-8 bg-gradient-to-r from-blue-600 to-purple-600 rounded-lg flex items-center justify-center shadow-lg shadow-blue-500/20"><Home className="w-5 h-5 text-white" /></div>
                      <span className="text-lg font-bold text-gray-800">RealBox</span>
                  </div>
                  <div className="flex gap-6 text-sm text-gray-500 mb-4 md:mb-0">
                      <a href="#features" className="hover:text-blue-600 transition-colors">Features</a>
                      <a href="#pricing" className="hover:text-purple-600 transition-colors">Pricing</a>
                  </div>
                  <p className="text-sm text-gray-400">&copy; {new Date().getFullYear()} RealBox. All rights reserved.</p>
              </div>
          </div>
        </footer>

        {/* Mobile CTA Bar */}
        {showMobileCta && (
          <div className="md:hidden fixed bottom-0 left-0 right-0 bg-white/80 backdrop-blur-xl border-t border-white/30 p-4 z-50 animate-slide-in-bottom">
            <button 
              onClick={handleGetStarted}
              className="w-full bg-gradient-to-r from-blue-600 to-purple-600 text-white px-6 py-3 rounded-xl text-base font-semibold hover:shadow-lg hover:shadow-blue-500/25 transition-all duration-300"
            >
              Get Started Free
            </button>
          </div>
        )}
      </div>
    </div>
  );
}
