---
routeAlias: 'integration-api-donnees'
---

# Intégration d'API et Gestion des Données (2024/2025)

## Appels API avec React Query

```tsx
// hooks/useProfiles.ts
import { useQuery } from '@tanstack/react-query';

export function useProfiles() {
  return useQuery({
    queryKey: ['profiles'],
    queryFn: async () => {
      const response = await fetch('https://api.example.com/profiles');
      if (!response.ok) {
        throw new Error('Erreur réseau');
      }
      return response.json();
    }
  });
}
```

---

## Utilisation dans un composant

```tsx
// app/(tabs)/home.tsx
export default function Home() {
  const { data: profiles, isLoading, error } = useProfiles();

  if (isLoading) {
    return <LoadingSpinner />;
  }

  if (error) {
    return <ErrorMessage message={error.message} />;
  }

  return (
    <View className="flex-1">
      <Swiper
        cards={profiles}
        renderCard={(profile) => (
          <ProfileCard {...profile} />
        )}
      />
    </View>
  );
}
```

---

## Mutations avec React Query

```tsx
// hooks/useLikeProfile.ts
import { useMutation, useQueryClient } from '@tanstack/react-query';

export function useLikeProfile() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: async (profileId: string) => {
      const response = await fetch(`https://api.example.com/like/${profileId}`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        }
      });
      return response.json();
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['matches'] });
    },
  });
}
```

---

## Stockage local avec MMKV

```tsx
// utils/storage.ts
import { MMKV } from 'react-native-mmkv';

export const storage = new MMKV();

// Stockage de données
export function storeData(key: string, value: any) {
  storage.set(key, JSON.stringify(value));
}

// Récupération de données
export function getData(key: string) {
  const value = storage.getString(key);
  return value ? JSON.parse(value) : null;
}
```

---

## Hook personnalisé pour le stockage persistant

```tsx
// hooks/usePersistedState.ts
import { useState, useEffect } from 'react';
import { storage } from '../utils/storage';

export function usePersistedState<T>(key: string, initialValue: T) {
  const [state, setState] = useState<T>(() => {
    const storedValue = storage.getString(key);
    return storedValue ? JSON.parse(storedValue) : initialValue;
  });

  useEffect(() => {
    storage.set(key, JSON.stringify(state));
  }, [key, state]);

  return [state, setState] as const;
}
```

---

## Configuration de l'API

```tsx
// services/api.ts
import axios from 'axios';

export const api = axios.create({
  baseURL: process.env.EXPO_PUBLIC_API_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Intercepteur pour le token
api.interceptors.request.use((config) => {
  const token = storage.getString('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

---

## Gestion des erreurs

```tsx
// components/ErrorBoundary.tsx
import { isRouteErrorResponse, useRouteError } from 'expo-router';

export function ErrorBoundary() {
  const error = useRouteError();

  if (isRouteErrorResponse(error)) {
    return (
      <View className="flex-1 items-center justify-center p-4">
        <Text className="text-xl font-bold">
          {error.status} {error.statusText}
        </Text>
        <Text className="mt-2 text-gray-600">{error.data?.message}</Text>
      </View>
    );
  }

  return (
    <View className="flex-1 items-center justify-center p-4">
      <Text className="text-xl font-bold">Oops!</Text>
      <Text className="mt-2 text-gray-600">
        Une erreur inattendue s'est produite
      </Text>
    </View>
  );
}
```

---

# Exercice : Intégration API de profils

Créez une application de rencontre qui :

1. Utilise React Query pour la gestion des données
2. Implémente un système de cache avec MMKV
3. Gère les erreurs avec ErrorBoundary
4. Utilise des indicateurs de chargement

---

## Solution de l'exercice

```tsx
// app/(tabs)/matches.tsx
import { useMatches } from '../../hooks/useMatches';
import { ErrorBoundary } from '../../components/ErrorBoundary';

export default function Matches() {
  const { data: matches, isLoading } = useMatches();

  if (isLoading) {
    return <LoadingView />;
  }

  return (
    <View className="flex-1 p-4">
      <FlatList
        data={matches}
        renderItem={({ item }) => (
          <MatchCard match={item} />
        )}
        keyExtractor={(item) => item.id}
      />
    </View>
  );
}

// Définir le boundary pour la route
Matches.ErrorBoundary = ErrorBoundary;
```

Cette version modernisée utilise les meilleures pratiques actuelles pour la gestion des données dans une application React Native/Expo.