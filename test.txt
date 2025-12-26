"use client";

import { useEffect, useState, ReactNode } from "react";
import { useRouter } from "next/navigation";
import axios from "axios";
import { useSession } from "next-auth/react";

export default function ProtectedLayout({ children }: { children: ReactNode }) {
  const [loading, setLoading] = useState(true);
  const router = useRouter();
  const { data: session, status } = useSession();

  useEffect(() => {
    // If NextAuth reports an authenticated session, we're done.
    if (status === "authenticated") {
      setLoading(false);
      return;
    }

    // If session is loading, wait.
    if (status === "loading") {
      setLoading(true);
      return;
    }

    // Fallback to the original JWT-based endpoint for compatibility.
    axios
      .get("/api/me")
      .then(() => setLoading(false))
      .catch(() => {
        router.push("/sign-in");
      });
  }, [router, status]);

  if (loading) {
    return (
      <div className="dark text-white h-170 flex justify-center items-center">
        Checking authentication...
      </div>
    );
  }

  return <>{children}</>;
}
