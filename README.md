# Eligibility-Form-Validation-
live demo link
https://v0-frontend-application-development-vvgq3b.vercel.app/
"use client"

import { zodResolver } from "@hookform/resolvers/zod"
import { useForm } from "react-hook-form"
import { z } from "zod"

import { Button } from "@/components/ui/button"
import { Form, FormControl, FormDescription, FormField, FormItem, FormLabel, FormMessage } from "@/components/ui/form"
import { Input } from "@/components/ui/input"
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select"
import { useToast } from "@/components/ui/use-toast"
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card"
import { Checkbox } from "@/components/ui/checkbox"

const formSchema = z.object({
  fullName: z.string().min(2, {
    message: "Name must be at least 2 characters.",
  }),
  age: z.string().refine((val) => !isNaN(Number(val)) && Number(val) >= 17 && Number(val) <= 24, {
    message: "Age must be between 17 and 24 years",
  }),
  education: z.string({
    required_error: "Please select your education level",
  }),
  isStudent: z.boolean(),
  university: z.string().optional(),
  gpa: z.string().refine((val) => !isNaN(Number(val)) && Number(val) >= 0 && Number(val) <= 4, {
    message: "GPA must be between 0 and 4",
  }),
})

export default function EligibilityForm() {
  const { toast } = useToast()

  const form = useForm<z.infer<typeof formSchema>>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      fullName: "",
      age: "",
      education: "",
      isStudent: false,
      university: "",
      gpa: "",
    },
  })

  function onSubmit(values: z.infer<typeof formSchema>) {
    // Check eligibility criteria
    const age = Number(values.age)
    const gpa = Number(values.gpa)
    const isEligible = age >= 17 && age <= 24 && gpa >= 3.0 && values.isStudent && values.education === "undergraduate"

    toast({
      title: isEligible ? "You are eligible!" : "Not eligible",
      description: isEligible
        ? "You meet all the requirements for the program."
        : "Sorry, you don't meet all the eligibility criteria. Requirements: Age 17-24, Active undergraduate student, GPA ≥ 3.0",
      variant: isEligible ? "default" : "destructive",
    })
  }

  return (
    <Card className="w-full max-w-2xl mx-auto">
      <CardHeader>
        <CardTitle>Eligibility Check</CardTitle>
        <CardDescription>Please fill out this form to check your eligibility for the program.</CardDescription>
      </CardHeader>
      <CardContent>
        <Form {...form}>
          <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-6">
            <FormField
              control={form.control}
              name="fullName"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Full Name</FormLabel>
                  <FormControl>
                    <Input placeholder="Enter your full name" {...field} />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />

            <FormField
              control={form.control}
              name="age"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Age</FormLabel>
                  <FormControl>
                    <Input placeholder="Enter your age" type="number" {...field} />
                  </FormControl>
                  <FormDescription>You must be between 17 and 24 years old.</FormDescription>
                  <FormMessage />
                </FormItem>
              )}
            />

            <FormField
              control={form.control}
              name="education"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Education Level</FormLabel>
                  <Select onValueChange={field.onChange} defaultValue={field.value}>
                    <FormControl>
                      <SelectTrigger>
                        <SelectValue placeholder="Select your education level" />
                      </SelectTrigger>
                    </FormControl>
                    <SelectContent>
                      <SelectItem value="highschool">High School</SelectItem>
                      <SelectItem value="undergraduate">Undergraduate</SelectItem>
                      <SelectItem value="graduate">Graduate</SelectItem>
                    </SelectContent>
                  </Select>
                  <FormMessage />
                </FormItem>
              )}
            />

            <FormField
              control={form.control}
              name="isStudent"
              render={({ field }) => (
                <FormItem className="flex flex-row items-start space-x-3 space-y-0">
                  <FormControl>
                    <Checkbox checked={field.value} onCheckedChange={field.onChange} />
                  </FormControl>
                  <div className="space-y-1 leading-none">
                    <FormLabel>I am currently an active student</FormLabel>
                  </div>
                </FormItem>
              )}
            />

            {form.watch("isStudent") && (
              <>
                <FormField
                  control={form.control}
                  name="university"
                  render={({ field }) => (
                    <FormItem>
                      <FormLabel>University/Institution</FormLabel>
                      <FormControl>
                        <Input placeholder="Enter your university name" {...field} />
                      </FormControl>
                      <FormMessage />
                    </FormItem>
                  )}
                />

                <FormField
                  control={form.control}
                  name="gpa"
                  render={({ field }) => (
                    <FormItem>
                      <FormLabel>Current GPA</FormLabel>
                      <FormControl>
                        <Input placeholder="Enter your GPA (0-4 scale)" {...field} />
                      </FormControl>
                      <FormDescription>Must have a minimum GPA of 3.0</FormDescription>
                      <FormMessage />
                    </FormItem>
                  )}
                />
              </>
            )}

            <Button type="submit" className="w-full">
              Check Eligibility
            </Button>
          </form>
        </Form>
      </CardContent>
    </Card>
  )
}

