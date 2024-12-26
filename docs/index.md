---
title: Welcome to my blog
---
MERRY CHRISTMAS TEAMD3

import { useEffect } from 'react';
import { gsap } from 'gsap';

const HomePage = () => {
  useEffect(() => {
    gsap.from(".hero-text", { opacity: 0, y: 50, duration: 1 });
  }, []);
  
  return (
    <div>
      <h1 className="hero-text text-4xl">Welcome to My Site</h1>
    </div>
  );
};

export default HomePage;
