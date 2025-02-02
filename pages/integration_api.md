---
routeAlias: 'integration-api-donnees'
---

# Intégration API pour l'application de rencontre

## Types et configuration

```tsx
// types/profile.ts
export interface Profile {
  id: string;
  name: {
    first: string;
    last: string;
  };
  picture: {
    large: string;
    medium: string;
  };
  dob: {
    age: number;
  };
  location: {
    city: string;
    country: string;
  };
}
```

---

## Hook personnalisé pour les profils

```tsx
// hooks/useProfiles.ts
import { useQuery } from '@tanstack/react-query';

export function useProfiles(count: number = 10) {
  return useQuery({
    queryKey: ['profiles'],
    queryFn: async (): Promise<Profile[]> => {
      const response = await fetch(
        `https://randomuser.me/api/?results=${count}&inc=name,picture,dob,location&nat=fr`
      );
      
      if (!response.ok) {
        throw new Error('Erreur lors du chargement des profils');
      }

      const { results } = await response.json();
      return results.map((profile: any) => ({
        ...profile,
        id: profile.login.uuid
      }));
    },
    staleTime: 5 * 60 * 1000, // Cache de 5 minutes
  });
}
```

---

## Utilisation dans le Swiper

```tsx
// app/(tabs)/home.tsx
import { useProfiles } from '../../hooks/useProfiles';

export default function HomeScreen() {
  const swiperRef = React.useRef<Swiper<Profile>>(null);
  const { data: profiles, isLoading, error } = useProfiles(20);

  if (isLoading) {
    return <LoadingView />;
  }

  if (error) {
    return <ErrorView error={error} />;
  }

  return (
    <SafeAreaView className="bg-background flex-1">
      <View className="flex-1 px-4">
        <Swiper
          ref={swiperRef}
          cards={profiles}
          renderCard={(profile) =>
            profile ? (
              <ProfileCard
                name={`${profile.name.first} ${profile.name.last}`}
                age={profile.dob.age}
                location={`${profile.location.city}, ${profile.location.country}`}
                image={profile.picture.large}
              />
            ) : null
          }
          onSwipedLeft={(cardIndex) => {
            console.log(`Disliked ${profiles[cardIndex].name.first}`);
          }}
          onSwipedRight={(cardIndex) => {
            console.log(`Liked ${profiles[cardIndex].name.first}`);
          }}
          backgroundColor="transparent"
          stackSize={10}
          // ... autres props du Swiper
        />
      </View>
    </SafeAreaView>
  );
}
```

---

## Gestion du cache et mise à jour

```tsx
// hooks/useUpdateProfile.ts
import { useMutation, useQueryClient } from '@tanstack/react-query';

export function useUpdateProfile() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: async (profileId: string) => {
      // Simuler un délai réseau
      await new Promise(resolve => setTimeout(resolve, 300));
      return profileId;
    },
    onSuccess: (profileId) => {
      // Mettre à jour le cache en retirant le profil
      queryClient.setQueryData(['profiles'], (oldData: Profile[]) => 
        oldData.filter(profile => profile.id !== profileId)
      );

      // Si le cache est vide, recharger de nouveaux profils
      const profiles = queryClient.getQueryData(['profiles']) as Profile[];
      if (profiles?.length < 5) {
        queryClient.invalidateQueries({ queryKey: ['profiles'] });
      }
    },
  });
}
```

---

## Composants d'UI optimisés

```tsx
// components/LoadingView.tsx
export function LoadingView() {
  return (
    <View className="flex-1 items-center justify-center">
      <ActivityIndicator size="large" color="#FF6B6B" />
      <Text className="mt-4 text-gray-600">
        Recherche de profils...
      </Text>
    </View>
  );
}

// components/ErrorView.tsx
export function ErrorView({ error, onRetry }: { error: Error; onRetry: () => void }) {
  return (
    <View className="flex-1 items-center justify-center p-4">
      <Text className="text-xl font-bold text-red-500">
        Oops!
      </Text>
      <Text className="mt-2 text-center text-gray-600">
        {error.message}
      </Text>
      <Button
        onPress={onRetry}
        className="mt-4 bg-primary px-6 py-2 rounded-full"
      >
        <Text className="text-white font-medium">Réessayer</Text>
      </Button>
    </View>
  );
}
```

---

Cette implémentation simplifiée :
- Utilise uniquement React Query avec fetch
- Gère le cache et les mises à jour optimistes
- Recharge automatiquement quand il reste peu de profils
- Inclut une gestion d'erreur et de chargement élégante
- Reste performante grâce au staleTime et au cache

Le tout sans dépendance supplémentaire autre que React Query.