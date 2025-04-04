FitTrack Pro - Dokümantasyon
Genel Bakış
FitTrack Pro, fitness ve sağlık takibi için geliştirilmiş kapsamlı bir web uygulamasıdır. Bu dokümantasyon, uygulamamızın yapısını, kullanılan teknolojileri ve bileşenlerini açıklamaktadır.

Teknoloji Yığını
FitTrack Pro, modern web geliştirme teknolojileri kullanılarak oluşturulmuştur:

Frontend
React: UI bileşenlerini oluşturmak için kullanılan JavaScript kütüphanesi
TypeScript: Tip güvenli kod yazmak için kullanılan JavaScript üst kümesi
Vite: Hızlı geliştirme ve derleme için modern build aracı
Tailwind CSS: Hızlı UI geliştirmek için kullanılan utility-first CSS framework
Shadcn/UI: UI komponentleri için React bileşen kütüphanesi
TanStack React Query: Sunucu state yönetimi ve veri fetching için kullanılan kütüphane
Wouter: Hafif ve performanslı routing kütüphanesi
DND Kit: Sürükle-bırak özellikler için kullanılan kütüphane
Lucide React: Modern ve özelleştirilebilir ikon kütüphanesi
GSAP (GreenSock Animation Platform): Gelişmiş animasyonlar için kullanılan kütüphane
Framer Motion: UI animasyonları için kullanılan kütüphane
Zod: Şema doğrulaması için kullanılan kütüphane
Backend
Express: Node.js web uygulaması için kullanılan framework
Drizzle ORM: TypeScript ile SQL veritabanı etkileşimleri için ORM
PostgreSQL: Veri depolama için kullanılan ilişkisel veritabanı
Passport.js: Kimlik doğrulama için kullanılan middleware
Express Session: Oturum yönetimi için kullanılan middleware
Uygulama Yapısı
Uygulama, klasik bir fullstack yapıya sahiptir:

project/
├── client/                    # Frontend kodu
│   ├── src/                   # Kaynak kodları
│   │   ├── components/        # Yeniden kullanılabilir UI bileşenleri
│   │   ├── context/           # React context yapıları
│   │   ├── hooks/             # Özel React hooks
│   │   ├── layouts/           # Sayfa düzenleri
│   │   ├── lib/               # Yardımcı fonksiyonlar ve konfigürasyonlar
│   │   ├── pages/             # Sayfa bileşenleri
│   │   │   ├── auth/          # Kimlik doğrulama sayfaları
│   │   │   ├── expert/        # Uzman dashboard sayfaları
│   │   │   ├── landing/       # Karşılama sayfaları
│   │   │   └── user/          # Kullanıcı dashboard sayfaları
│   │   ├── App.tsx            # Ana uygulama bileşeni ve router
│   │   └── main.tsx           # Uygulama giriş noktası
│   └── index.html             # Ana HTML dosyası
├── server/                    # Backend kodu
│   ├── controllers/           # Route işleyicileri
│   ├── middleware/            # Express middleware'leri
│   ├── services/              # İş mantığı ve harici servisler
│   ├── index.ts               # Server giriş noktası
│   └── routes.ts              # API endpoint tanımları
└── shared/                    # Frontend ve backend arasında paylaşılan kod
    └── schema.ts              # Veri modeli şemaları
Dashboard Mimarisi
User Dashboard
Kullanıcılar (sporcular) için geliştirilen dashboard, fitness ilerlemesini ve sağlık metriklerini takip etmek için kapsamlı bir arabirim sunar.

Önemli Bileşenler
DashboardProvider: Dashboard durumunu (aktif sekme, görüntüleme modu) yöneten bir Context API uygulaması
MainLayout: Tüm dashboard sayfaları için standart header, sidebar ve içerik düzeni
Tab Sistemi: Farklı bölümler arası geçiş için sekme tabanlı navigasyon
Ana Veri Hookları
useWorkouts: Antrenman verilerini çeken ve yöneten hook
useNutrition: Beslenme verilerini çeken ve yöneten hook
useBodyMetrics: Vücut ölçüm verilerini çeken ve yöneten hook
// Örnek: useWorkouts hook'u
export function useWorkouts() {
  const queryClient = useQueryClient();
  
  const { data: workouts = [], isLoading: isWorkoutsLoading } = useQuery<Workout[]>({
    queryKey: ['/api/workouts'],
  });
  
  const createWorkoutMutation = useMutation({
    mutationFn: async (newWorkout: InsertWorkout) => {
      const response = await apiRequest<Workout>('/api/workouts', {
        method: 'POST',
        body: JSON.stringify(newWorkout),
      });
      return response;
    },
    onSuccess: (workout: Workout) => {
      queryClient.invalidateQueries({ queryKey: ['/api/workouts'] });
      toast({
        title: "İşlem başarılı",
        description: "Yeni antrenman oluşturuldu",
      });
    },
    onError: (error: Error) => {
      toast({
        title: "Hata",
        description: error.message,
        variant: "destructive",
      });
    },
  });
  
  // Diğer mutasyon ve fonksiyonlar...
  
  return {
    workouts,
    isLoading: isWorkoutsLoading,
    createWorkout: createWorkoutMutation.mutate,
    // Diğer işlevler...
  };
}
Expert Dashboard
Uzmanlar (antrenörler, diyetisyenler) için hazırlanan dashboard, danışan yönetimi, program oluşturma ve ilerleme analizi sağlar.

Önemli Bileşenler
DraggableWidget: Sürükle-bırak yöntemiyle özelleştirilebilen dashboard widget'ları
ClientsTabContent: Danışan yönetimi için kullanılan sekme içeriği
ProgramTabContent: Program oluşturma için kullanılan sekme içeriği
ProgressTabContent: İlerleme analizi için kullanılan sekme içeriği
// Örnek: DraggableWidget bileşeni
function DraggableWidget({ id, children }: { id: string; children: React.ReactNode }) {
  const {
    attributes,
    listeners,
    setNodeRef,
    transform,
    transition,
  } = useSortable({ id });
  const style = {
    transform: CSS.Transform.toString(transform),
    transition,
  };
  return (
    <div 
      ref={setNodeRef} 
      style={style} 
      className="relative group"
    >
      <div 
        {...attributes} 
        {...listeners}
        className="absolute right-3 top-3 h-6 w-6 cursor-move opacity-0 group-hover:opacity-80 transition-opacity duration-200 flex items-center justify-center rounded-full bg-primary-100 text-primary z-10"
      >
        <span className="sr-only">Move</span>
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M18 11V8L22 12L18 16V13H13V18H16L12 22L8 18H11V13H6V16L2 12L6 8V11H11V6H8L12 2L16 6H13V11H18Z" fill="currentColor"/>
        </svg>
      </div>
      {children}
    </div>
  );
}
Veri Mimarisi
Uygulama, Drizzle ORM ile PostgreSQL veritabanını kullanan bir veri mimarisi kullanır.

Ana Veri Modelleri
users: Kullanıcılar (hem sporcular hem uzmanlar)
workouts: Antrenman planları
exercises: Egzersiz kütüphanesi
workout_exercises: Antrenman-egzersiz ilişkileri
foods: Besin kütüphanesi
meal_plans: Beslenme planları
body_metrics: Vücut ölçüm verileri
// Örnek: Şema tanımları (shared/schema.ts)
export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  username: text("username").notNull().unique(),
  email: text("email").unique(),
  password: text("password").notNull(),
  name: text("name"),
  role: text("role").default("user"),
  createdAt: timestamp("created_at").defaultNow(),
});
export const workouts = pgTable("workouts", {
  id: serial("id").primaryKey(),
  userId: integer("user_id").references(() => users.id),
  name: text("name").notNull(),
  description: text("description"),
  createdAt: timestamp("created_at").defaultNow(),
});
// Diğer tablo tanımları...
Kimlik Doğrulama ve Yetkilendirme
Uygulama, Passport.js ve Express Session kullanarak tam kapsamlı bir kimlik doğrulama sistemi sunar:

// Passport.js konfigürasyonu (services/auth/auth.service.ts)
passport.use(
  new LocalStrategy(async (username, password, done) => {
    try {
      const user = await storage.getUserByUsername(username);
      if (!user || !(await comparePasswords(password, user.password))) {
        return done(null, false);
      } else {
        return done(null, user);
      }
    } catch (error) {
      return done(error);
    }
  }),
);
API Endpoints
API, RESTful prensiplerine uygun bir şekilde tasarlanmıştır:

// API routes tanımı (server/routes.ts)
export async function registerRoutes(app: Express): Promise<Server> {
  // Auth routes
  app.post("/api/register", register);
  app.post("/api/login", login);
  app.post("/api/logout", logout);
  app.get("/api/user", getCurrentUser);
  
  // Workouts
  app.get("/api/workouts", withAuth(getUserWorkouts));
  app.post("/api/workouts", withAuth(createWorkout));
  app.delete("/api/workouts/:id", withAuth(deleteWorkout));
  
  // Exercises
  app.get("/api/exercises", getAllExercises);
  app.get("/api/exercises/muscle/:group", getExercisesByMuscleGroup);
  
  // Body metrics
  app.get("/api/body-metrics", withAuth(getUserBodyMetrics));
  app.post("/api/body-metrics", withAuth(createBodyMetric));
  
  // Diğer endpoint'ler...
}
UI Bileşenleri
Uygulama, Shadcn/UI ve Tailwind CSS tabanlı bir tasarım sistemi kullanır:

Button: Etkileşimli butonlar
Card: Bilgi kartları
Tabs: Sekme grupları
Badge: Durum etiketleri
Progress: İlerleme çubukları
Dialog: Modal pencereler
Calendar: Takvim seçiciler
Chart: Veri görselleştirme grafikleri
// Örnek: Card kullanımı
<Card className="bg-card shadow-sm">
  <CardHeader className="pb-2">
    <CardTitle className="text-lg font-semibold flex items-center">
      <Users className="mr-2 h-5 w-5 text-primary" />
      Genel İstatistikler
    </CardTitle>
  </CardHeader>
  <CardContent>
    <div className="grid grid-cols-2 gap-4">
      <div className="space-y-2">
        <p className="text-sm text-muted-foreground">Toplam Antrenman</p>
        <p className="text-2xl font-bold">{workouts.length}</p>
      </div>
      {/* Diğer istatistikler */}
    </div>
  </CardContent>
</Card>
Expert Dashboard İnovasyonları
Expert Dashboard, aşağıdaki özelliklere sahiptir:

Özelleştirilebilir Dashboard: DND Kit ile sürükle-bırak widget düzenleme
Real-time Veri Görüntüleme: TanStack Query ile otomatik veri güncellemeleri
İlerleyen Geliştirilecek Özellikler:
Three.js ile 3D ilerleme görselleştirmeleri
Danışan-uzman iletişim platformu
GSAP ile animasyonlu performans metrikleri
Program oluşturucu arayüzü
Depolama ve Önbellek Stratejisi
TanStack Query ile verimli bir önbellek stratejisi uygulanmıştır:

// API istek yardımcı fonksiyonları (lib/queryClient.ts)
async function throwIfResNotOk(res: Response) {
  if (!res.ok) {
    let errorMessage = "API error";
    try {
      const errorData = await res.json();
      errorMessage = errorData.message || errorMessage;
    } catch (e) {
      // JSON parse hatası, varsayılan mesajı kullan
    }
    throw new Error(errorMessage);
  }
}
export async function apiRequest<T>(
  path: string,
  init?: RequestInit
): Promise<T> {
  const res = await fetch(path, {
    ...init,
    headers: {
      ...init?.headers,
      'Content-Type': 'application/json',
    },
  });
  
  await throwIfResNotOk(res);
  return res.json();
}
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5, // 5 dakika
      refetchOnWindowFocus: true,
      retry: false,
      queryFn: getQueryFn({ on401: "throw" }),
    },
  },
});
Geliştirici Özellikleri
Dev Butonu: Uzman dashboard'una hızlı erişim için klavye kısayolu (Ctrl+Shift+D) ile aktifleştirilebilen bir erişim butonu
Theme Desteği: Karanlık/aydınlık mod desteği ve özelleştirilebilir tema
Responsive Tasarım: Mobil, tablet ve masaüstü ekranlar için optimize edilmiş UI
// Dev Butonu Kodu (client/src/App.tsx)
function DevButton() {
  const [visible, setVisible] = useState(true);
  const handleKeyPress = (event: KeyboardEvent) => {
    // Show/hide dev button on Ctrl+Shift+D
    if (event.ctrlKey && event.shiftKey && event.key === 'D') {
      setVisible(prev => !prev);
    }
  };
  useEffect(() => {
    window.addEventListener('keydown', handleKeyPress);
    return () => {
      window.removeEventListener('keydown', handleKeyPress);
    };
  }, []);
  if (!visible) return null;
  return (
    <a href="/expert/dashboard" className="fixed bottom-4 right-4 z-50">
      <Button size="sm" variant="outline" className="bg-background/80 backdrop-blur-sm border-primary">
        <Crown className="mr-2 h-4 w-4 text-primary" />
        Expert Dashboard
      </Button>
    </a>
  );
}
Sonuç
FitTrack Pro, modern web teknolojilerini kullanarak kapsamlı bir fitness takip çözümü sunar. Uygulama, hem sporcular hem de uzmanlar için özelleştirilmiş dashboard'lar içerir ve ileri düzey özellikler için genişletilebilir bir altyapı sağlar.

Bu dokümantasyon, uygulamanın mevcut durumunu ve kullanılan teknolojileri açıklar. Gelecek sürümler, daha fazla özellik ve iyileştirme ile güncellenecektir.